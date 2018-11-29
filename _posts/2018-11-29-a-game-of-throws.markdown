---
layout: post
title:  "A Game of Throws"
labels: [math, statistics, science]
mathjax: true
categories: Blog
---

I recently had the chance to mentor a high school student who was doing
an internship at the Max Planck institute for Nuclear Physics. Since
every project related to my actual work would require quite a solid knowledge
of Quantum Field Theory, we could not do much there together.
Still, I wanted to give him an idea of how does it feel like to work in
theoretical physics, I decided to make him work on problems related to statistics and
Monte Carlo simulations, which are easy enough to understand (at least,
intuitively) with the math knowledge of a high school student.

When we started reviewing together the basics of combinatorial calculus
(possibly one of my favourite topics in mathematics) I started assigning
him some exercises, which more often than not I would invent on the spot.
At some point, I assigned him the following innocuous
looking problem:

> Suppose you are playing a game where you throw a die $$N$$ times, with $$N$$ larger than two,
> and you win if in the sequence of throws you obtain at least two sixes in a row.
> What's the probability to get such a winning sequence?

For instance,  1**66**45 is a winning sequence, 
whereas 65626 is a losing one.

![Examples of sequences](/assets/pics/dices/winlose.svg){: .center-image}

For reasonably small values of $$N$$, a computer can quickly enumerate every possible sequence
of throws, and from that it is possible to calculate a probability. For larger
values of $$N$$, instead, it is trivial to find an approximate solution using a
Monte Carlo technique: just build sequences of throws at random and divide the number of
winning ones by the number of total sequences generated.

Is it possible, however, to find an analytical solution to this problem?
In order to do it, one should be able to count all the sequences of $$N$$ throws
which contain at least two sixes in a row
and divide that number by $$6^N$$, the total number of sequences of $$N$$ throws of a die.

The first solution my student proposed me was along the following lines: let us
count the number of sequences we have when we fix the initial throws to be two sixes,
and let the other ones be arbitrary.

![Sequences where the first two throws result in sixes, while the others are
arbitrary](/assets/pics/dices/firstmethodA.svg){: .center-image}

We can then move the sequence of two sixes to be not at the beginning,
but starting from the second throw, and so on until the sequence is at the end.

![Two sixes in a row are fixed, the other throws are
arbitrary](/assets/pics/dices/firstmethodB.svg){: .center-image}

Since we fixed two throws of the die, there are $$N-2$$ throws which can yield arbitrary results
(marked in the figures with asterisks), for a total of $$6^{N-2}$$ possibilities.
In this way, one could think that the total number of winning sequences
are $$(N-1) 6^{N-2}$$. Thus, the answer to the problem seems

$$P_{\text{wrong}}(N) = \frac{(N-1)6^{N-2}}{6^N }= \frac{N-1}{36}. \tag{1}$$

This, however, cannot be the true solution, because
probabilities must be smaller than one, while $$P_1(N)$$ becomes larger than one for
$$N$$ larger than $$37$$. This suggests that the solution is wrong and is, in fact,
a symptom of double-counting. It's easy to see what's wrong with this solution if we
enumerate explicitly all the possible sequences for $$N = 3$$.

![Example of double counting](/assets/pics/dices/problem.svg){: .center-image}

By counting twice some combinations, we overestimate our chances to win.

Thus, we need a different strategy to count. It is not really easy to fix the initially-proposed
method, so let's start from scratch: we will build the solution inductively!
Assume that we already know that for $$N$$
throws there are $$A (N) $$ possible sequences which contain two sixes in a row;
how does this number change when we throw the die one extra time?

Of course, if we already had a winning sequence,
whatever outcome we have on the new throw will result in a winning sequence;
since there are six possible outcomes, we have $$6 A(N) $$ sequences of this kind.

![](/assets/pics/dices/correctA.svg){: .center-image}

Moreover, in some cases a new throw can create new winning sequences! In
fact, if we had a losing sequence which ended with a $$6$$, if the new throw results in
a $$6$$ we will obtain a winning sequence. Graphically, we have:

![](/assets/pics/dices/correctB.svg){: .center-image}

How many such sequences are there? Well, since the last two throws must be fixed (i.e.,
two sixes) we only need to calculate the number of
losing sequences of size $$N-1$$; this number is given by subtracting $$A(N-1)$$, the number
of winning sequences of size $$N-1$$, from $$6^N$$, the number of
total sequences of size $$N-1$$.

As a result, we obtain the following equation for the total number of winning sequences
in $$N+1$$ throws:

$$ A(N+1) = 6 A(N) + 6^N - A(N-1) \tag{2}$$

This is starting to look quite nice: if we calculate by brute force two consecutive values
of $$A(N)$$, for instance $$A(2) = 1$$ and $$A(3) = 11$$, we can get every following value
of $$A(N)$$ by successive applications of Equation 2. For large values of $$N$$, it is
**MUCH** more easy to evaluate $$N$$ times Equation 2 rather than building each of the $$6^N$$
sequences.

| $$N$$ | $$A(N)$$ | $$6^N$$|
|-------|----------|--------|
|     2 | 1        |  36    |
|     3 | 11       |  216   |
|     4 | 101      |  1296  |
|     5 | 811      |  7776  |
|     6 | 6061     |  46656 |
|     7 | 43331    |  279936|

But this is not over; we can do better! It is actually possible to "solve" Equation 2
for $$A(N)$$, and get immediately the answer to the starting problem. I will not delve
into detail here, but it is easy to check that the solution is

$$ A(N) = 6^N +\frac{5}{2} (3-2\sqrt{2})^N (1+\sqrt{2})
   	      +\frac{5}{2} (3+2\sqrt{2})^N (1-\sqrt{2}). \tag{3}$$

Notice how we have an apparently complicated combination of sums of powers of irrational
numbers, and yet $$A(N)$$ is always an integer number, because it satisfies Eq. (3)!
Small wonders of mathematics ¯\\_(ツ)_/¯


And so, the probability of winning at the Game of Throws is

$$P(N) = \frac{A(N)}{6^N }\tag{4}$$

Notice especially that the asymptotic behaviour is correct this time: for very large $$N$$,
we can approximate $$A(N)$$ as $$A(N) \approx 6^N$$, thus the probability of winning
approaches one, as one could expect intuitively.

![Plot of the probability of winning as a function of the
number of throws](/assets/pics/dices/plot.svg){: .center-image}

It is interesting to see that for small number of throws the above mentioned
double counting problem is not severe, so for $$N < 10$$ the "probability"
$$P_{\text{wrong}}(N)$$ could still be a useful estimate.

Overall, I think it was a great exercise; it allowed me (and especially my student) to
do quite nice calculations, both analytically and numerically, on an applied problem,
and learn how to solve recurrence equations, which can be much more easy to understand
by a high school student with respect to differential equations while sharing with them
quite some properties.