---
layout: post
title:  "Deep Learning, Heteroscedasticity, and TensorFlow Probability"
categories: [machine learning, statistics, TensorFlow]
image: assets/images/tf_logo.png
mathjax: True
---

It is very often the case that Machine Learning models
calculate point estimates of quantities of interest:
given the number of rooms, size, and location, they
predict that a house costs $$x$$ Euros. 

In order to make decisions based on these models,
however, it is almost mandatory to quantify
the confidence we have in their predictions;
this is especially true in business scenarios, where
large deviations from a prediction could cost an enormous
amount of money. In some cases we might even be interested
in learning not just expectation values, but the
entire probability distribution of a random variable.

In this short post, I will show how easy it is to combine
Deep Learning and Probabilistic Modeling with an
awesome new module for TensorFlow, called TensorFlow
Probability.

# Learning the parameters of a distribution

Consider the following artificially generated dataset:


and imagine your task is to predict $$Y$$ given the
feature $$X$$. Probably the first approach one would
try is linear regression, which would result in the
following predictions:



From this plot, it is clear that the predictions for
$$X \approx 0$$ are more likely to be correct with
respect to the ones for $$X$$ away from $$0$$; this
kind of uncertainty is intrinsic in the data and
independent from our model. In particular, since
the variance of $$Y$$ varies with $$X$$, our data
shows [heteroscedasticity](https://en.wikipedia.org/wiki/Heteroscedasticity),
which can be quite a hassle if we want to quantify
the errors we expect in our predictions.


