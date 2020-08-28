---
layout: post
title:  "Some title here"
labels: [math, statistics]
mathjax: true
categories: [math, statistics]
image: assets/images/doge.jpg
---

With just a tiny bit of effort, it is easy to prove that

$$
\newcommand{\sinc}{\mathrm{sinc}}
\newcommand{\diff}{\mathrm{d}}
\int_0^\infty \diff x \, \sinc (x) = \frac{\pi}{2}
$$

and with some more patience, one can also prove that

$$
\begin{align*}
\int_0^\infty \diff x \,
\sinc(x) \, \sinc(x/3) &= \frac{\pi}{2} \\
\int_0^\infty \diff x \,
\sinc(x) \, \sinc(x/3) \, \sinc(x/5) &= \frac{\pi}{2} \\
\dots& \\
\int_0^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/13) &= \frac{\pi}{2}
\end{align*}
$$

Wow, this is quite unexpected! There seems to be a pattern here,
but when a [Maple](https://en.wikipedia.org/wiki/Maple_(software))
developer was playing with this kind of integrals he noticed
that

$$
\int_0^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/15)
\approx \frac{\pi}{2} - 2.31 \times 10^{-11} 
$$

While at first he attributed this problem to a bug in the code,
indeed after a few days he realized that this is indeed the
correct result. There are various works about the calculation and
interpretation of this kind of integrals
([[Borwein and Borwein, 2001]](https://link.springer.com/article/10.1023%2FA%3A1011497229317), [[Schmid, 2014]](https://www.ems-ph.org/journals/show_abstract.php?issn=0013-6018&vol=69&iss=1&rank=2)), but the
interpretation which in my opinion is most intuitive and easy to
generalize can be found in
[[Majumdar and Trizac, 2019]](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201).

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
p_i(x) = \langle x | \phi \rangle;
$$
after a random jump of maximum size $$a > 0$$, the distribution will in general be
given by another state, which we will define to be $$\hat{S}(a) | \phi \rangle$$. The 
resulting probability density will then be
$$
p_{i+1}(x) = \langle x |\hat{S}(a)| \phi \rangle.
$$

The time evolution of the system can then be obtained by repeated application
of the operator $$\hat{S}(a)$$. Therefore, the pdf for the position of 
the particle after $$N$$ steps is

$$
p_N(x) = \langle x | \hat{S}(a_N) \hat{S}(a_{N-1}) \dots \hat{S}(a_1) | 0 \rangle;
$$

by introducing completeness relations between each pair of operators we obtain
$$
p_N(x) = \int_{\mathbb{R}^{N-1}} \mathrm{d}x_1 \dots \mathrm{d}x_{N-1} 
\langle x | \hat{S}(a_N) | x_{N-1 \rangle}
\langle x_{N-1}| \hat{S}(a_{N-1}) | x_{N-2} \rangle
\dots
\langle x_1 | \hat{S}(a_1) | 0 \rangle.
$$

Notice that all of the factors appearing in the equation above are quite trivial 
to calculate, as in them $$\hat{S}$$ acts only on states where the particle is for
sure at a specific point. Therefore we have

$$
\langle x_i | \hat{S}(a) | x_{i-1} \rangle = \mathrm{U}(x_i; x_{i-1} - a, x_{i-1} + a)
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
\langle x | \hat{S}(a_N) | x_{N-1 \rangle}
\langle x_{N-1 |} \hat{S}(a_{N-1}) | x_{N-2 \rangle}
\dots
\langle x_1 | \hat{S}(a_1) | 0 \rangle.
$$


%%%%%%%%

The only paths which contribute to $$p_N(0)$$ are the ones that return to 
the origin in $$N$$ steps;
