---
layout: post
title:  "Censoring Features in Time Series Forecasting with Multi-Input LSTM Models"
labels: [statistics]
categories: Blog
---

# Introduction

In this post (and its associated notebook ... ) I want to 
illustrate a problem I have been thinking about in time series
forecasting with LSTMs, while simultaneously 
showing how to properly use some Tensorflow features which greatly
help in this setting.


Imagine the following: we have a time series (i.e., some values $$y(t_i)=y_i$$ at times
$$t_i$$), and we also have at each time step some auxiliary features $$\bm{X}(t_i) = \bm{X}_i$$
which we think are related with the values of $$y_i$$. Some of the $$\bm{X}$$s 
might be known for all time (think of them as predetermined features, like in which month is
the time $$t_i$$)

Let's start with a practical example of a time series and look at the Bike sharing dataset;
there we can find for each hour the amount of bikes rented by customers,
together with other features such as whether a certain day was a national holiday, and 
which day of the week was it. Now, if we wanted to forecast the demand for the bike sharing
service, we would really like to use the information contained in the dataset;



