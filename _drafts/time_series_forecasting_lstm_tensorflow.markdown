---
layout: post
title:  "Time Series Forecasting with an LSTM Encoder/Decoder in TensorFlow 2.0"
labels: [deep learning, machine learning]
categories: Blog
---

# Introduction

In this post (and its associated notebook ... ) I want to 
illustrate a problem I have been thinking about in time series
forecasting with LSTMs, while simultaneously 
showing how to properly use some Tensorflow features which greatly
help in this setting (specifically, the `tf.data.Dataset` class and Keras' 
functional API).

Imagine the following: we have a time series, i.e., some values $$y(t_i)=y_i$$ at times
$$t_i$$, and we also have at each time step some auxiliary features $$\bm{X}(t_i) = \bm{X}_i$$
which we think are related with the values of $$y_i$$. Some of the $$\bm{X}$$s 
might be known for all time (think of them as predetermined features, like whether time $$t_i$$
is a national holiday) whereas others are random and quite difficult to forecast in advance (say,
the price of a Microsoft stock at time $$t_i$$).

Thus, if we want to train a model to forecast the future values of the time series we cannot
use every column of $$\bm{X}$$, but rather we need to censor the features we would not be able to 
know at prediction time. 
In this way, the shape of our (see the figure below)


...

For these kinds of tasks, a pretty straightforward procedure would be to use an autoregressive
model of some kind (like 
[ARMAX](https://en.wikipedia.org/wiki/Autoregressive%E2%80%93moving-average_model#Generalizations)); 
these models allow us to take into account
autocorrelations in a time series, and also can accept the deterministic features in the future
(typically called "exogenous variables").
One limitation of ARMAX is that it is a linear model, and also one needs to specify the order of 
autocorrelations to be taken into account parametrically. LSTMs, instead, can learn nonlinear 
patterns, and can take into account autocorrelations in a nonparametric way!


In order to experiment a bit with LSTMs in the context of time series forecasting, an idea I wanted 
to try is the following: let's encode past observations in a latent space, and then use the 
encoded past as a sort of "context" to then perform forecasts with an LSTM. 
% footnote analogie con machine translation


In order to better illustrate this problem and my proposed solution, let's consider in the 
following section a concrete example.

# Example: BALBALBA Dataset

Let's start with a practical example of a time series and look at the Bike sharing dataset;
there we can find for each hour the amount of bikes rented by customers,
together with other features such as whether a certain day was a national holiday, and 
which day of the week was it. For the sake of simplicity, 
let's skip a lot of data cleaning/feature engineering steps one could apply to this dataset, 
and just load it via the following:

...code...

As mentioned before, we want to feed the "past" plus some deterministic features in the future 
to a Keras model and get back some predictions for the future; in order to make Keras accept this data, 
it has to be reshaped in an appropriate way. We need to split it into "windows" where each row is a 
time step and each column correspond to a feature. Moreover, if we want to split the training into
multiple batches we need to aggregate all these windows in a 
tensor of shape $$(n_{batches}, n_{timesteps}, n_{features})$$.

Since the data is already in a Pandas DataFrame, we could easily do these steps with a mix of Pandas
methods (`DataFrame.rolling()` & co.) and then transform the data into Numpy arrays 
(which TensorFlow can then ingest).
I try to show here an approach which I like more, which can work seamlessly for much larger datasets
which do not fit in memory and has a very clean API: we initialize a `tf.data.Dataset` object from the 
data and then transform it via TensorFlow builtin functions. So, we can just define a function that 
returns the desired form of the dataset like this:

...code...

and then use it to generate tf.data.Dataset objects to feed to TensorFlow:

...code...

Notice that aside from extracting windows out of the dataset and selecting the correct portions out
of them, we also randomize the order of training examples we show to the model, batch it, and 
[prefetch](https://www.tensorflow.org/api_docs/python/tf/data/Dataset#prefetch) it. 

Now that the data is prepared, we need to define the model. The specifics of the neural architecture
I am using here are not particularly optimized. The basic idea behind it is that I wanted to try to
encode past observations in a latent space, and then use the encoded past as a sort of "context"
to then perform forecasts with an LSTM. % footnote analogie con machine translation REPET

...figura...

Thus, we can define the model pictured above via the Keras functional API as follows:

...code...


In order to train the model, since we are using tf.data.Dataset objects for the data, 
we just need to call

...code...




# Bonus: What if we want to forecast probability distributions?



# Explainability of the model

Well, that's a topic for another day :-) 


# Conclusion

