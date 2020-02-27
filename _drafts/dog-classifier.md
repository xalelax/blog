---
layout: post
title:  "Transfer learning: let's build a dog breed classifier!"
labels: [deep learning, machine learning]
categories: [deep learning, machine learning]
image: assets/images/encdec.png
---

# Introduction

In this post, I want to showcase how simple it has become to perform transfer learning
with the help of modern libraries. I will use TensorFlow, but the situation looks pretty
similar with other frameworks. In particular, I will load a Dataset from the TensorFlow
Dataset library % check name
and download a pretrained model for object classification. 
We will then attach a custom layer on top of pretrained
model, and train it for the 



Transfer learning is based on the idea that the feature a network learns for a problem
can be reused for a variety of other tasks; %aggiungere refs
As an example for how this can be done, let's consider image classification problems!

The first few layers of a convolutional neural network learn to perform pretty simple
tasks, such as recognizing edges at different angles, corners, etc... But the deeper we 
go towards the network output, the more abstract learned features become (from patterns 
and shapes all the way to human faces). This intuitively suggests that if we already have a 
network which can recognize,say, furniture (chairs, desks, and so on) from pictures, 
in order to solve a different problem in computer vision, such as recognizing the model
of a car from a picture, for sure we will need to learn in the final layer 
the quite "abstract" features which correspond to a Ferrari Testarossa, but on the other 
hand we don't need to teach the network "how to see" from scratch. 
The features built in the first few %specificare few
layers of the furniture detection model can be "recycled" for car model detection; 
we just need to cut away a few of the final layers where the network learned features
which are useful for furniture detection but not for car detection.

# Dog breed classifier

Let's start by downloading the Stanfords Dog Dataset from 

# Conclusions

In this tutorial, I hope I was able to give a minimal working example for transfer learning
with TensorFlow, 
