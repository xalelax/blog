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
and download a model pretrained on the imagenet dataset. We will then attach a custom layer on top of the pretrained
model, and train it for 



Transfer learning is based on the idea that the feature a network learns for a problem
can be reused for a variety of other tasks; one of the most 

The first few layers of a convolutional neural network learn to perform pretty simple
tasks, such as recognizing edges at different angles, corners, etc., and the more one goes
"deep" towards the final layers, the more abstract features are learned (from patterns and shapes
all the way to human faces). 

# Dog breed classifier

Let's start by downloading the Stanfords Dog Dataset from 

# Conclusions
