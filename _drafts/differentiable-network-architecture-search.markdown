---
layout: post
title:  "Auto-Deeplab and Differentiable Network Architecture Search"
labels: [deep learning, math]
categories: Blog
---

I recently stumbled (thanks a lot, Noam!)
across a very interesting paper,
"Auto-DeepLab: Hierarchical Neural Architecture Search for Semantic
Image Segmentation" by C. Liu et al.; the authors were able to find a network
capable of state-of-the-art performance on a very hard problem, image segmentation,
in only 3 GPU days (with an NVIDIA Tesla P100). This is especially impressive
since comparable methods require typically something like a thousand GPU days on
the same hardware.

This great achievement sparked my interest in a quite interesting technique
called Differentiable Network Architecture Search (DNAS). I decided to
write a post about it to try to illustrate it in a simple way, and
include links to the relevant literature in order to do a little service to
readers who would like to start researching the topic.

If I had to sum up DNAS in one sentence, I would say that it is a technique
which enables us to learn simultaneously the architecture of a neural network 
AND its weights by transforming the NAS problem, typically discrete,
into a continuous one, which can then be solved via gradient descent.

For definiteness, in the following I will show how DNAS can be applied to Convolutional
Neural Networks (CNNs), but keep in mind that a lot of the concepts I will consider in the
following can be effectively used for other architectures too.

# Differentiable DNAS and DARTS

In order to build a CNN, we typically need to specify its architecture first;
this basically means choosing many parameters, like size/depth of each filter, which operations
are applied on each layer, what is connected to what, and so on; for now,
let us imagine we have encoded all this information in a vector of
hyperparameters $$\alpha$$. 

Notice that for the kind of parameters I described,
$$\alpha$$ will contain only discrete variables; *this can be quite problematic*, because
if we want to find the best architecture for a given problem we really want to
solve the following optimization problem:

$$
\begin{align}
\min_\alpha \, &\mathcal{L}_{\text{val}}(\alpha, w^*(\alpha)) \tag{1}\\
\text{with }
w^*(\alpha) &= \underset{w}{\operatorname{argmin}}\, \mathcal{L}_{\text{train}}(\alpha, w),
\tag{2}
\end{align}
$$

where $$\mathcal{L}_{\text{val}}$$ and $$\mathcal{L}_{\text{train}}$$ are the loss
function calculated, respectively, on either the validation or the training set,
and depend on the architecture of the network $$\alpha$$ and on the weights
$$w$$.

In other words, we want to find the architecture which, after being trained on
the training set, gives the best results on the validation set. Since $$\alpha$$
is composed of discrete variables, Eq. (1) cannot be solved immediately via
gradient descent. One could use reinforcement learning or evolutionary algorithms
for Eq. (1),
but notice that this entails training a large number of "useless" networks (via
gradient descent on Eq. (2)), and this inner loop can be prohibitively
time-consuming.

In the [DARTS paper](https://arxiv.org/abs/1806.09055) (H. Liu et al.),
the authors present a solution to this problem via continuous relaxation.
This was not an entirely new idea (%todo: refs),
but whereas previous approaches used it in order to fine tune specific 
parameters, like filter sizes and number of filters of a specific architecture,
in [DARTS](https://arxiv.org/abs/1806.09055) the search space is made of cells 
with potentially highly complex topologies. Cells are then stacked
to form the entire CNN.

Specifically, a cell is a directed acyclic graph, consisting of an ordered
sequence of $$N$$ nodes $$x^{(i)}$$. Each node is associated with a
tensor, and each directed 
edge $$(i,j)$$ (connecting node $$i$$ to node $$j$$) is associated 
with some operation $$o^{(i,j)}$$ on $$x^{(i)}$$. Moreover, it is assumed that 
a cell has two tensors as inputs and one tensor as output;
consistently with common choices for CNNs (zoph 2018), the two
tensors chosen as an input in DARTS are the outputs from the 
previous two cells. The output of a cell is found by applying
a reduction operation (tipically concatenation) to all of the 
intermediate nodes. %todo: aggiungere footnote su risoluzione che va tenuta costante

Thus, as we described, each node in a cell is given by

$$
x^{(j)} = \sum_{i < j} o^{(i,j)}(x^{(i)}).
$$

In order to transform the

# Auto-Deeplab

A limitation of DNAS in DARTS is the fact that it does not search for 
the best architecture at a network level: only the block architecture is
found by DNAS, and then multiple identical blocks are stacked
