---
layout: post
title:  "Some title here"
labels: [math, statistics]
mathjax: true
categories: [math, statistics]
image: assets/images/doge.jpg
---


With just a tiny bit of effort, it is easy to prove that

...

and with some more patience, one can also find out that

...


Now, I guess because of my upbringing as a physicist,
whenever I see that something is true for N = 1 and N = 2, I
am already starting to believe it is true for all $$n$$s. There seems
to be a pattern here, and indeed there is, but when ... and ...
first tried to calculate this integrals for N = ...,
they found that
...

While at first they attributed this problem to a bug in the code,
indeed that is the correct result. 

# Interpretation via a Path Integral

Let's start by noticing that the
sinc function is nothing but the Fourier transform of the
uniform distribution
...

and therefore, according to the ... theorem, a product of sinc functions
is dual to convolutions of uniform distributions. The values of the
integrals ..., will then just correspond to one specific value
of their Fourier transform.

..................

Let's consider a particle which starts at $$x = 0$$ and that moves at
discrete time steps by performing a sequence of
(uniformly distributed) random jumps, i.e.,

...

Notice that, although the change in the position due to each jump is always
uniformly distributed, the parameters of the uniform distribution are allowed
to change between jumps; in particular, the maximum distance that can be jumped
with the first jump is $$a_1$$, with the second one is $$a_2$$, and so on.

%%%%%%%%%5

We can associate to each jump an operator $$\hat{S}(a)$$ in the following way:
suppose that at some generic point in time the probability density for the 
position $$x$$ of the particle is 
$$
p_i(x) = \braket{x | \phi};
$$
after a random jump of maximum size $$a > 0$$, the distribution will in general be
given by another state, which we will define to be $$\hat{S}(a) \ket{\phi}$$. The 
resulting probability density will then be
$$
p_{i+1}(x) = \bra_{x}\hat{S}(a)\ket{\phi}.
$$

The time evolution of the system can then be obtained by repeated application
of the operator $$\hat{S}(a)$$. Therefore, the pdf for the position of 
the particle after $$N$$ steps is

$$
p_N(x) = \bra{x} \hat{S}(a_N) \hat{S}(a_{N-1}) \dots \hat{S}(a_1) \ket{0};
$$

by introducing completeness relations between each pair of operators we obtain
$$
p_N(x) = \int_{\mathbb{R}^{N-1}} \mathrm{d}x_1 \dots \mathrm{d}x_{N-1} 
\bra{x} \hat{S}(a_N) \ket{x_{N-1}}
\bra{x_{N-1}} \hat{S}(a_{N-1}) \ket{x_{N-2}}
\dots
\bra{x_1} \hat{S}(a_1) \ket{0}.
$$

Notice that all of the factors appearing in the equation above are quite trivial 
to calculate, as in them $$\hat{S}$$ acts only on states where the particle is for
sure at a specific point. Therefore we have

$$
\braket{x_i | \hat{S}(a) | x_{i-1} } = \mathrm{U}(x_i; x_{i-1} - a, x_{i-1} + a)
= \mathrm{U}(x_i - x_{i-1}; - a, + a),
$$

% define uniform here? no, probably above

and so we can write
$$
p_N(x) = \int_{\mathbb{R}^{N-1}} \mathrm{d}x_1 \dots \mathrm{d}x_{N-1} 
\mathrm{U}(x - x_{N-1}; - a_N, a_N)
\mathrm{U}(x_{N-1} - x_{N-2}; - a_{N-1}, a_{N-1})
\dots 
\mathrm{U}(x_1; - a_1, + a_1)
\bra{x} \hat{S}(a_N) \ket{x_{N-1}}
\bra{x_{N-1}} \hat{S}(a_{N-1}) \ket{x_{N-2}}
\dots
\bra{x_1} \hat{S}(a_1) \ket{0}.
$$


%%%%%%%%

The only paths which contribute to $$p_N(0)$$ are the ones that return to 
the origin in $$N$$ steps;
