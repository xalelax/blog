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
\int_{-\infty}^\infty \diff x \, \sinc (x) = \pi
$$

and with some more patience, one can also prove that

$$
\begin{align*}
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3) &= \pi \\
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3) \, \sinc(x/5) &= \pi \\
\dots& \\
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/13) &= \pi
\end{align*}
$$

Wow, this is quite unexpected! There seems to be a pattern here,
but when a [Maple](https://en.wikipedia.org/wiki/Maple_(software))
developer was playing with this kind of integrals he noticed
that

$$
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/15)
\approx \pi - 4.6 \times 10^{-11} < \pi
$$

While at first he attributed this problem to a bug in the code,
indeed after a few days he realized that this is indeed the
correct result. There are various papers about the calculation and
interpretation of this kind of integrals
([[Borwein and Borwein, 2001]](https://link.springer.com/article/10.1023%2FA%3A1011497229317), [[Schmid, 2014]](https://www.ems-ph.org/journals/show_abstract.php?issn=0013-6018&vol=69&iss=1&rank=2)), but the
one which in my opinion is most intuitive and has potential
for generalization is
[[Majumdar and Trizac, 2019]](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201), where they link Borwein integrals
to random walks. Heavily borrowing from 
[[Majumdar and Trizac, 2019]](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201), I will explain what is happening
under the hood of these integrals with the tool that I think
makes it clearer (especially to physicists): the path integral.


# Interpretation via a Path Integral

Let's start from a seemingly unrelated[^1] problem:
consider a particle which starts at $$x_0 = 0$$ and that moves at
discrete time steps by performing a sequence of
(uniformly distributed) random jumps, i.e.,

$$
x_i = x_{i-1} + \delta_i,
$$

where $$x_i$$ is the position of the particle at the $$i$$-th time, and
$$\delta_i$$ is a random variable uniformly distributed between $$-a_i$$ and
$$a_i$$ (assume $$a_i > 0$$). Notice that, although the change in the position
due to each jump is always
uniformly distributed, the parameters of the uniform distribution are allowed
to change between jumps; in particular, the maximum distance that can be jumped
with the first jump is $$a_1$$, with the second one is $$a_2$$, and so on.



We can associate to each jump a unitary operator $$\hat{S}(a)$$ in the following way:
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
\langle x | \hat{S}(a_N) | x_{N-1} \rangle
\langle x_{N-1}| \hat{S}(a_{N-1}) | x_{N-2} \rangle
\dots
\langle x_1 | \hat{S}(a_1) | 0 \rangle.
$$

Notice that all of the factors appearing in the equation above are quite trivial 
to calculate, as in them $$\hat{S}$$ acts only on states where the particle is for
sure at a specific point. Therefore we have

$$
\langle x_i | \hat{S}(a) | x_{i-1} \rangle = \mathrm{U}_{ x_{i-1} - a}^{ x_{i-1} + a}(x_i)
= \mathrm{U}_{ - a}^{ + a}(x_i - x_{i-1}),
$$

where we introduced for the continuous uniform distribution the symbol

$$
\mathrm{U}_a^b(x) =
\begin{cases}
\frac{1}{b-a}, \quad &\mathrm{if} \quad  a \leq x \leq b \\
0 \quad &\mathrm{otherwise}
\end{cases}
$$

and so we can write

$$
p_N(x) = \int_{\mathbb{R}^{N-1}} \mathrm{d}x_1 \dots \mathrm{d}x_{N-1} 
\mathrm{U}_{ - a_N}^{ + a_N}(x - x_{N-1})
\mathrm{U}_{ - a_{N-1}}^{ +a_{N-1}}(x_{N-1} - x_{N-2})
\dots 
\mathrm{U}_{ - a_1}^{ + a_1}(x_1).
$$

By noticing that the Fourier transform of the uniform distribution is 

$$
\int \diff x \, \mathrm{U}_{ - a_i}^{ + a_i}(x) e^{- i k x} = \sinc(a_i k), 
$$

and that according to the [Convolution Theorem](https://en.wikipedia.org/wiki/Convolution_theorem)
convolutions are mapped to products, we obtain

$$
p_N(x) = \int \frac{\diff k}{2\pi} \, \prod_{i=1}^N \sinc(a_i k) \, e^{- i k x},
$$

And so, in order to calculate the Borwein integrals we were originally interested in
we just need to calculate

$$
2 \pi p_N(0) =  \int \diff k \, \prod_{i=1}^N \sinc(a_i k),
$$

which in the "path-integral" picture we developed above means we need to integrate
only over paths which start from the origin and, after $$N$$ steps, end up being in
the origin again.

Now, notice the following: what happens if we perform the substitution

$$
\mathrm{U}_{ - a_1}^{ + a_1}(x_1) \longrightarrow \frac{1}{2a_1},
$$

i.e., if we forget that the first step of our particle was bounded in size
by $$a_1$$? Sure, we will be introducing some "spurious" paths in the
path integral, but then we could write $$p_N(0)$$ as 

$$
\begin{multline}
p_N(0) "="
\int_{-\infty}^\infty \diff x_{N-1}
\mathrm{U}_{ - a_N}^{ a_N}(-x_{N-1})
\int_{-\infty}^\infty \diff x_{N-2}
\mathrm{U}_{ - a_{N-1}}^{ a_{N-1}}(x_{N-1} - x_{N-2})
\int_{-\infty}^\infty \diff x_{N-3}
\dots \\ \dots
\int_{-\infty}^\infty \diff x_1
\mathrm{U}_{ - a_2}^{ a_2}(x_2-x_1)
\frac{1}{2a_1}
\end{multline}
$$

(where we put the equal sign between quotation marks to higlight the fact
that this is not true in general). Notice then that the integral over
$$\diff x_1$$ is equal to $$1$$, independent of $$x_2$$; therefore also the
integral over $$\diff x_2$$ is unity, and so on, so that we simply have

$$
p_N(0) "=" \frac{1}{2a_1}.
$$

The question then becomes: when is this result correct (i.e., when can we
omit the ugly quotation marks in the formula above)?

The spurious paths we effectively introduced with our procedure are
the ones for which $$x_1 > a_1$$. But suppose that

$$
a_1 > \sum_{i=2}^N a_i;
$$

in this case, the spurious paths cannot contribute to $$p_N(0)$$, as even if
the particle would jump in the direction of the origin at each step, the jump sizes
after the first one would be bounded by the $$a_i$$s, and could arrive at most at a
distance

$$
|x_1| - \sum_{i=2}^N a_i > 0
$$

away from the origin. In some way, one can interpret this as a sort of causal argument:
if the information that $$\mathrm{U}_{ - a_1}^{ + a_1}(x)$$ has a boundary cannot reach
the origin in $$N$$ steps, the probability density at the origin will be constant and
invariant as the time steps. When "spurious" paths are able to backtrack to the origin,
the value of the probability density must become strictly smaller as, in a way, it would
stay constant only by adding the (positive) contribution of the "spurious" paths.
Therefore, for Borwein integrals, we do have that

$$
\begin{align*}
&\frac{1}{3} + \frac{1}{5} + \dots + \frac{1}{13} \approx 0.955 < 1 \\
&\Longrightarrow
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/13) = 2 \pi p_N(0) = \pi,
\end{align*}
$$

but also, when the information about the boundedness of the first jump reaches the origin, that

$$
\begin{align*}
&\frac{1}{3} + \frac{1}{5} + \dots + \frac{1}{15} \approx 1.022 > 1 \\
&\Longrightarrow
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/15) < \pi,
\end{align*}
$$


-------------------------------------------------------------------
[^1]:
    Keeping in mind that the $$\sinc$$ function is the
    Fourier transform of the [rectangular function](https://en.wikipedia.org/wiki/Rectangular_function)
    (and therefore of the [continuous uniform distribution](https://en.wikipedia.org/wiki/Uniform_distribution_(continuous)),
    and that products are mapped to convolutions when Fourier-transformed, you might already see
    that this problem is not so unrelated after all!