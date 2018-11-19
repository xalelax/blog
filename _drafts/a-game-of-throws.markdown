---
layout: post
title:  "A game of throws"
categories: [math, statistics, science]
---

I recently had the chance to mentor a high-school student who was doing
an internship at the Max Planck institute for Nuclear Physics. Since
every project related to my actual work would require quite a solid knowledge
of Quantum Field Theory, we could not do much there together.
Still, I wanted to give him an idea of how does it feel like to work in
theoretical physics, I decided to make him work on statistics and
Monte Carlo simulations, which are easy enough to understand (at least,
intuitively) with the math knowledge of a high-school student.

When we started reviewing together the basics of combinatorial calculus
(possibly one of my favourite topics in mathematics) I started assigning
him some exercises. At some point, I assigned him the following innocuous
looking problem:

>Suppose you throw a die N times, with N larger than two. What is the probability
>that at some point in the sequence of throws you obtain two sixes in a row.

In order to solve this problem, one needs to count all the sequences of N throws
which contain at least two sixes in a row, such as

%todo: immagine

and divide that number by 6^N, the total number of sequences of N throws of a die.

The first solution my student proposed me was the following: let us represent the
sequence of throws as a table such as

%todo: immagine

Then, we can fix the initial throws to be two sixes, and the other ones are
arbitrary. We can then move the sequence of two sixes to be not at the beginning,
but starting from the second throws, and so on until the sequence is at the end.

%todo: immagine

(here, an asterisk represents an arbitrary outcome for the die throw).

In this way, one could think that the total number of sequences with two sixes in a
row are (N-1) 6^(N-2). Thus, the answer to the problem seems
P_1(N) = (N-1)6^(N-2)/6^N = (N-1)/36. This, however, cannot be the true solution, because
probabilities must be smaller than one, while P_1(N) becomes larger than one for
N larger than 37. This suggests that the solution is wrong and is, in fact,
a symptom of double-counting. It's easy to see what's wrong with this solution if we consider
the example N = 3.

%todo: immagine
%todo: finire spiegazione

Thus, we need a different strategy to count; a method which I particularly like is to
build the solution inductively! 


