---
layout: post
title:  "Deep Learning, Heteroscedasticity, and TensorFlow Probability"
categories: [machine learning, statistics, TensorFlow]
image: assets/images/tf_logo.png
mathjax: True
---

It is very often the case that Machine Learning models
calculate point estimates of quantities of interest;
a typical example would be:
given the number of rooms, size, and location of a house, they
predict that it costs $$x$$ Euros.

In order to make real-world decisions based on these models,
however, it is almost mandatory to quantify
the confidence we have in their predictions;
this is especially true in business scenarios, where
large deviations from a prediction could cost an enormous
amount of money. In some cases we might even be interested
in learning not just expectation values, but the
entire probability distribution of a random variable.

In this short post, I will show how easy it is to combine
Deep Learning and Probabilistic Modeling with an
awesome new module for TensorFlow, called
[TensorFlow Probability](https://www.tensorflow.org/probability).


# Learning the parameters of a distribution

Consider the following artificially generated dataset:

![Scatter plot of the data](/assets/pics/tfp/scatter.svg){: class="col-9"}

and imagine your task is to predict $$Y$$ given the
feature $$X$$. Probably the first approach one would
try is linear regression, which would result in the
following predictions (shown in red):

![Linear regression on the data](/assets/pics/tfp/linreg.svg){: class="col-9"}

From this plot, it is clear that the predictions for
$$X \approx 0$$ are "closer to the truth" with
respect to the ones for $$X$$ away from $$0$$; the
uncertainty we observe in this case is intrinsic in the data and
independent from our model. In particular, since
the variance of $$Y$$ varies with $$X$$, our data
exhibits [heteroscedasticity](https://en.wikipedia.org/wiki/Heteroscedasticity),
which can be quite a hassle if we want to quantify
the errors we expect to make in our predictions.

Let's start with the code I used to perform linear regression
with TensorFlow:

```python
import tensorflow as tf
import tensorflow_probability as tfp

tfd = tfp.distributions

model = tf.keras.Sequential([
            tf.keras.layers.Dense(1)
])

model.compile(optimizer=tf.optimizers.Adam(learning_rate = 0.1),
              loss='mean_squared_error')

model.fit(X, Y, epochs=40);
```

Although for now this code is unnecessarily complicated, it is
equivalent to linear regression: I defined a model with only a single
perceptron `tf.keras.layers.Dense(1)`, which by default has a linear
activation function and can take bias into account (that is, for $$X = 0$$
the model will not necessarily predict $$Y = 0$$).

Let's try now to make the model output not just a number, but a distribution;
for the sake of definiteness, let's try to learn the "best" normal distribution
which models the random variable $$Y$$ for each value of $$X$$.
In order to do so, notice that we cannot use anymore the mean squared error
as a loss function, since it is not a meaningful error metric in this case.
We will, instead, use the negative logarithm of likelihood between the
distributions given by our model vs the observed data; in this way, training
our model becomes essentially a maximum likelihood estimation of parameters!

Notice also that for each value of $$X$$ the normal distribution we want our
model to learn is parametrized by two numbers: mean and standard deviation.
Therefore, we should increase the number of neurons in the dense layer from
one to two, so that each perceptron will learn the functional dependency on
$$X$$ of one of the two parameters, and
then push its output into the distribution.

The way to implement these changes in TensorFlow Probability is very nice:
we can use a `tfp.layers.DistributionLambda` layer which works in pretty
much the same way as a "standard" Keras layer; in its argument, we
can plug a lambda function which takes parameters from the previous layers
of the network and returns a `tfp.Distribution`:

```python
model = tf.keras.Sequential([
            tf.keras.layers.Dense(2),
            tfp.layers.DistributionLambda(lambda t:
                tfd.Normal(loc   = t[...,:1],
                           scale = tf.math.softplus(0.005*t[...,1:])+0.001)
            )
])

negloglik = lambda y, p_y: -p_y.log_prob(y)

model.compile(optimizer=tf.optimizers.Adam(learning_rate = 0.1),
              loss=negloglik)
```
This code, a minimal alteration from the linear regression case, is able
to learn both the mean value and the variance of $$Y$$ given $$X$$.
After training, let's plot what this model has learnt:

![Linear regression with confidence](/assets/pics/tfp/conf1.svg){: class="col-9"}

Excellent! The green lines are set 2 standard deviations above and below the
mean (here with "standard deviation" and "mean" I intend the values of these
parameters which our model learnt). They seem to somewhat characterize our
confidence in the values of $$Y$$; can we improve on this?

Since we used only a single dense layer with linear activations before the
`tfp.layers.DistributionLambda`, both the estimated mean and estimated standard deviation
can only depend linearly on $$X$$, and this leads to an underestimation
of error in some regions and an overestimation in others.
By using a deeper neural network and
introducing nonlinear activation functions, however, we can learn more complicated
functional dependencies!

We can achieve this with just the addition of another dense layer
`tf.keras.layers.Dense(20,activation="relu")`
before the layer with two perceptrons, i.e., by defining the model as:

```python
model = tf.keras.Sequential([
            tf.keras.layers.Dense(20,activation="relu"),    
            tf.keras.layers.Dense(2),
            tfp.layers.DistributionLambda(lambda t:
                tfd.Normal(loc   = t[...,:1],
                           scale=tf.math.softplus(0.005*t[...,1:])+0.001)
            )
])
```

Finally, after fitting the data, we can obtain the following (much nicer) plot:

![Nonlinear confidence intervals](/assets/pics/tfp/conf2.svg){: class="col-9"}

This time, we can see how the confidence interval given by the two green lines
accurately assesses the variability of $$Y$$ given $$X$$!

# Conclusions

This was just one of the many incredible things made possible by TensorFlow
Probability in very few lines of code; I plan to show many more in the future
where I will use it for the study of time series.

In any case, I think that TensorFlow Probability really helps in bridging the
gap between probabilistic methods and neural networks, something I am extremely
interested in. Thus, I have the feeling this will hardly be the last time I
write about it. :-)


# Appendix

The dataset I analyzed was also generated by
TensorFlow Probability via a pretty cool feature which is present
only in its nightly build (as of the time of writing);
the method `tfp.distributions.JointDistributionSequential`.

It allows to build joint probability distributions starting from
elementary (and possibly interdependent) ones. The code
I wrote is the following:

```python
tfd = tfp.distributions

joint = tfd.JointDistributionSequential([
                 tfd.Uniform(low=-8, high=15),
      lambda x : tfd.Normal(loc=x, scale=abs(x)+3)
])
```

In order to get samples from this distribution, one needs
just to run the line `X, Y = joint.sample(2000)`; in my
view this is a very elegant way to work with distributions
in Python. Moreover, as we have seen above, `tfp.Distribution`
objects can be integrated seamlessly and in a modular way with
deep learning models.
