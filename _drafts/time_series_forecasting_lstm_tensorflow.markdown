---
layout: post
title:  "Time Series Forecasting with an LSTM Encoder/Decoder in TensorFlow 2.0"
labels: [deep learning, machine learning]
categories: Blog
---

# Introduction

In this post (and its associated [notebook](https://colab.research.google.com/drive/1-MaDm60lVXS_6cUBou7_oa5JwjXqpKlk)) I want to
illustrate a problem I have been thinking about in time series
forecasting with LSTMs, while simultaneously
showing how to properly use some Tensorflow features which greatly
help in this setting (specifically, the `tf.data.Dataset` class and Keras'
functional API).

Imagine the following: we have a time series, i.e., a sequence of values $$y(t_i)=y_i$$ at times
$$t_i$$, and we also have at each time step some auxiliary features $$\boldsymbol{X}(t_i) = \boldsymbol{X}_i$$
which we think are related with the values of $$y_i$$. Some of the $$\boldsymbol{X}$$s
might be known for all time (think of them as predetermined features, like whether time $$t_i$$
is a national holiday) whereas others are random and quite difficult to forecast in advance (say,
the price of a Microsoft stock at time $$t_i$$).

Thus, if we want to train a model to forecast the future values of the time series we cannot
use every column of $$\boldsymbol{X}$$, but rather we need to censor the features we would not be able to
know at prediction time, as shown in the picture below.

![Structure of data in forecasting](/assets/pics/encdec/table_time_series.svg){: class="col-9"}

For these kinds of tasks, a pretty straightforward procedure would be to use an autoregressive
model of some kind (like
[ARMAX](https://en.wikipedia.org/wiki/Autoregressive%E2%80%93moving-average_model#Generalizations));
these models allow us to take into account
autocorrelations in a time series, and also can accept the deterministic features in the future
(typically called "exogenous variables").
One limitation of ARMAX is that it is a linear model, and also one needs to specify the order of
autocorrelations to be taken into account parametrically. LSTMs, instead, can learn nonlinear
patterns, and are able to take into account autocorrelations in a nonparametric way!


So, let's try this idea: let's encode past observations in a latent space, and then use the
encoded past as a sort of "context" to then perform forecasts with an LSTM[^1].
In order to better illustrate this problem and my proposed solution, let's consider in the
following section a concrete example.

# Example: Bike Sharing

Let's start with a practical example of a time series and look at the
[UCI Bike Sharing Dataset](https://archive.ics.uci.edu/ml/datasets/bike+sharing+dataset);
there we can find for each hour the amount of bikes rented by customers of a bike sharing
service in Washington DC,
together with other features such as whether a certain day was a national holiday, and
which day of the week was it. The dataset can be downloaded with the following
bash commands:
```bash
wget https://archive.ics.uci.edu/ml/machine-learning-databases/00275/Bike-Sharing-Dataset.zip
unzip Bike-Sharing-Dataset.zip -d bike_data
```

Our goal is to be able to predict the total bike usage for
the week following prediction time. For the sake of simplicity,
let's skip a lot of data cleaning/feature engineering steps one could apply to this dataset,
and just load it via the following:

```python
import pandas as pd

df = pd.read_csv('bike_data/hour.csv', index_col='instant',
                 parse_dates=['dteday'])

def select_columns(df):
    cols_to_keep = ['cnt',
                    'temp',
                    'hum',
                    'windspeed',
                    'yr',
                    'mnth',
                    'hr',
                    'holiday',
                    'weekday',
                    'workingday']

    df_subset = df[cols_to_keep]

    return df_subset


```

As mentioned before, we want to feed the "past" plus some deterministic features in the future
to a Keras model and get back a forecast; in order to make Keras accept this data,
it has to be reshaped in an appropriate way. We need to split it into "windows" where each row is a
time step and each column correspond to a feature. Moreover, if we want to split the training into
multiple batches we need to aggregate all these windows in a
tensor of shape $$(n_{batches}, n_{timesteps}, n_{features})$$.

Since the dataset is already loaded in a Pandas DataFrame, we could easily do these steps with a mix of Pandas
methods (`DataFrame.rolling()` & co.) and then transform the data into Numpy arrays
(which TensorFlow can then ingest).
I try to show here an approach I like more, that can work seamlessly for much larger datasets
which do not fit in memory and has a very clean API: we initialize a `tf.data.Dataset` object from the
data and then transform it via TensorFlow builtin functions. So, we can just define a function that
returns the desired form of the dataset like this:

...code...

Notice that aside from extracting windows out of the dataset and selecting the correct portions out
of them, we also randomize the order of training examples we show to the model, batch it, and
[prefetch](https://www.tensorflow.org/api_docs/python/tf/data/Dataset#prefetch) it.
Via the `generate_dataset` function we can create `tf.data.Dataset` objects
to feed to TensorFlow:

...code...

This kind of train/cv/test split is most likely quite biased (as I already wrote
in [another article on my blog](/time-nested-cv-with-sklearn)), but for the sake of simplicity
let's keep it this way.

Now that the data is prepared, we need to define the model. The specifics of the neural architecture
I am using here are not particularly optimized, but they follow from the basic idea that I want to
encode past observations in a latent space, and then use the encoded past as a sort of "context"
to then perform forecasts with an LSTM.

![Structure of data in forecasting](/assets/pics/encdec/architecture.svg){: class="col-9"}

Thus, we can define the model pictured above via the Keras functional API as follows:

...code...


In order to train the model, since we are using tf.data.Dataset objects for the data,
we just need to call

...code...

We can check for the mean squared error on the test set by calling `model.evaluate(test_windowed)`
and we obtain for these Hyperparameters a mean absolute error of about 
$$75$$ bikes; not too bad!


# Bonus: What if we want to forecast probability distributions?

As I [previously argued on my blog](/tensorflow-heteroscedasticity), point predictions
are not the full story, and it is often of uttermost importance to be able to 
predict *probability distributions*[^2]. Luckily, since we built our model with 
TensorFlow, we can just make a slight modification to the head of the neural network
and attach to it a TensorFlow Probability distribution layer; thus, we can define
the model via

...code...

The loss function now cannot be MSE or Huber anymore, because the model returns distributions.
A natural choice here is to do maximize likelihood, which is equivalent to 
minimizing negative log-likelihood. So, the model can be trained in the following way:

...code...

And after a while we can obtain reasonable-looking forecasts.



# Explainability of the model

Well, that's a topic for another day :-)


# Conclusion

This was just a very simple application; I did not optimize the model at all,
but I think one can build upon this to achieve interesting results. Especially,
better data cleaning and feature engineering would help a lot.

Another idea I would like to try for forecasting is 
[attention-based models](https://arxiv.org/abs/1706.03762)




-------------------------------------------------------------------

[^1]: This kind of technique is very common in machine translation; see %%%%%%%%

[^2]: I recently read a very accessible book on the problems which can arise 
      in businesses when ignoring probability distributions which I wish more
      people would read and understand. It is called *The Flaw of Averages*, by
      Sam L. Savage, and I wholeheartedly recommend it :-) 
