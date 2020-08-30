---
layout: post
title:  "Borwein Integrals interpreted via Path Integrals"
labels: [math, statistics]
mathjax: true
categories: [math, statistics]
image: assets/images/borwein.png
---

The ability to see common patterns and analogies, to conjecture universal
laws starting from the particular things we can experience,
is a powerful tool, which arguably is one of the foundations of science.
Although we rely constantly on our inductive abilities,
the cases when our intuition is spectacularly wrong teaches us valuable lessons,
in the same way optical illusions can make us understand how our visual
perception works.

My favorite example about this is the (apparent) pattern one can see
in Borwein integrals (from the father and son who popularized this
kind of integrals, in
[[Borwein and Borwein, 2001]](https://link.springer.com/article/10.1023%2FA%3A1011497229317) );
my interest in them was rekindled by a very cool recent article
by [Majumdar and Trizac](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201),
where they link the strange properties of Borwein integrals to probability distributions
associated with some random walks, and with an argument essentially about causality they
are able to explain what is happening "under the hood".

Let's then start our study of these interesting integrals.
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

Wow, there seems to be a pattern here,
and one could maybe conjecture a handful of reasons why
all these kinds of integrals **must** be equal to $$\pi$$.

The plot thickens after a [Maple](https://en.wikipedia.org/wiki/Maple_(software))
developer was playing with this kind of integrals and he noticed
that

$$
\int_{-\infty}^\infty \diff x \,
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/15)
\approx \pi - 4.6 \times 10^{-11} < \pi,
$$

i.e., the value of the integral is slightly less than $$\pi$$.
While at first he attributed this problem to a bug in the code,
indeed after a few days he realized that this is indeed the
correct result. There are various papers about the calculation and
interpretation of this kind of integrals
([[Borwein and Borwein, 2001]](https://link.springer.com/article/10.1023%2FA%3A1011497229317), [[Schmid, 2014]](https://www.ems-ph.org/journals/show_abstract.php?issn=0013-6018&vol=69&iss=1&rank=2)), but the
one which in my opinion is most intuitive and has potential
for generalization is the above-mentioned
[[Majumdar and Trizac, 2019]](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201), where they link Borwein integrals
to random walks. Heavily borrowing from 
[[Majumdar and Trizac, 2019]](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201), I will explain what is happening
under the hood of these integrals with the tool that I think
makes essentially any kind of random process very clear (especially to physicists): the path integral.


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

At any point in time, the state of the particle can be in general associated
with the probability density of finding the particle at a certain position $$x$$;
therefore, states of this system live in the vector space of integrable functions on
the real axis.
I will use the [bra-ket notation](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation),
and write then elements of this vector space as $$| \phi \rangle$$, scalar products
as

$$
\langle \phi | \psi \rangle = \int_\mathbb{R} \diff x\, \phi(x) \psi(x). 
$$

States labeled by positions, like $$x$$ and $$x_i$$, are states where we are definitely sure
that the particle occupies a certain position, and are essentially Dirac deltas $$\delta(...)$$, i.e.,

$$
\langle \bar{x} | \psi \rangle = \int_\mathbb{R} \diff x \, \delta(x - \bar{x}) \psi(x) = \psi(\bar{x}),  
$$

and we can easily prove that they satisfy the completeness relation:

$$
\int_\mathbb{R} \diff \tilde{x} \,
\langle \phi  | \tilde{x} \rangle \langle \tilde{x} | \psi \rangle =
\int_\mathbb{R} \diff \tilde{x} 
\int_\mathbb{R} \diff x_1 
\int_\mathbb{R} \diff x_2 \,
\phi(x_1) \delta(x_1 - \tilde{x}) \delta(x_2 - \tilde{x}) \psi(x)
= \langle \phi | \psi \rangle,
$$

for any pair of vectors $$\{ \vert \phi\rangle, \vert \psi\rangle\}$$.

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
p_N(x) = \langle x | \hat{S}(a_N) \hat{S}(a_{N-1}) \dots \hat{S}(a_1) | x_0 = 0 \rangle;
$$

by introducing completeness relations between each pair of operators we obtain

$$
p_N(x) = \int_{\mathbb{R}^{N-1}} \mathrm{d}x_1 \dots \mathrm{d}x_{N-1} 
\langle x | \hat{S}(a_N) | x_{N-1} \rangle
\langle x_{N-1}| \hat{S}(a_{N-1}) | x_{N-2} \rangle
\dots
\langle x_1 | \hat{S}(a_1) | x_0 = 0 \rangle.
$$

Notice that all of the factors appearing in the equation above are quite trivial 
to calculate, as in them $$\hat{S}$$ acts only on states where the particle is for
sure at a specific point. This should not be surprising, as it is essentially a manifestation
of the [chain rule](https://en.wikipedia.org/wiki/Chain_rule_(probability)) for independent
events. Therefore we have

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
\int_\mathbb{R} \diff x \, \mathrm{U}_{ - a_i}^{ + a_i}(x) e^{- i k x} = \sinc(a_i k), 
$$

and that according to the [Convolution Theorem](https://en.wikipedia.org/wiki/Convolution_theorem)
convolutions are mapped to products, we obtain

$$
p_N(x) = \int_\mathbb{R} \frac{\diff k}{2\pi} \, \prod_{i=1}^N \sinc(a_i k) \, e^{- i k x},
$$

And so, in order to calculate the Borwein integrals we were originally interested in
we just need to calculate

$$
2 \pi p_N(0) =  \int_\mathbb{R} \diff k \, \prod_{i=1}^N \sinc(a_i k),
$$

which in the "path-integral" picture we developed above means we need to integrate
only over paths which start from the origin and, after $$N$$ steps, end up being in
the origin again.

Now, let's ask ourselves the following question: what happens if we perform the substitution

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
invariant with $$N$$ as the time steps. When "spurious" paths are able to backtrack to the origin,
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
\sinc(x) \, \sinc(x/3)\, \dots \, \sinc(x/15) < \pi.
\end{align*}
$$

# Generalizations

Notice how the initially given form of Borwein integrals, with odd integers appearing in the
arguments of $$\sinc$$ functions, was just smoke and mirrors: we could have chosen
any sequence of $$a_i$$s, provided that

$$
a_1 > \sum_{i=2}^N a_i,
$$

and the value of the integral would still be $$\pi/a_1$$. Therefore, from the proof above,
we might as well state that

$$
\int_{-\infty}^\infty \diff x \,
\prod_{i=1}^N \sinc(a_i x)  = \pi,
\qquad
a_1 > \sum_{i=2}^N a_i, \quad
a_i \in \mathbb{R}^+.
$$

In our proof also we never made use of the fact that the probability distribution of jumps after the first
one was uniform: we just needed them to have finite support and to be correctly normalized. So, for instance,
as the Fourier transform of Bessel functions of integer order $$\alpha$$ divided by their argument to the power
of $$\alpha$$ satisfy these properties,
we can also find the following nontrivial identities for Bessel functions:

$$
\int_{-\infty}^\infty \diff x \,
\sinc(a_1 x)
\prod_{i=2}^N
\frac{2^i i! J_\alpha(a_i x)}{2 \pi (a_i x)^\alpha} = \pi,
\qquad
a_1 > \sum_{i=2}^N a_i, \quad
a_i \in \mathbb{R}^+, \quad
\alpha \in \mathbb{N}.
$$

![Fourier transforms of Besse](/assets/pics/borwein/ftbessel.png){: class="col-12" style="text-align: center"}

...Or we can even get more general ones by mixing Bessel functions of different order: in the random walk picture,
that would amount to allowing jumps after the first one to be characterized by the Fourier transform of the
corresponding factor.

Even more, by considering discrete jumps instead of continuously-distributed ones,
one can introduce other trigonometric functions in the integrands. In 
[[Majumdar and Trizac, 2019]](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.123.020201),
they even find identities for multi-dimensional integrals and for other interesting mathematical problems,
such as proving when

$$
\int_{-\infty}^{+\infty} \diff{k} \prod_{i=1}^N f_i(a_i k) \stackrel{?}{=}
\sum_{k = -\infty}^{+\infty} \prod_{i=1}^N f_i(a_i k),
$$

by only using causal reasoning.

# Conclusion

I think I finally understood what is happening with Borwein integrals, and their connection with
random walks is deeply interesting in my opinion. The fact that the probability distribution is affected
by boundary effects (i.e, from the fact that the density is not constant over all the real numbers) only
when information from the boundary is carried by some paths is a great idea, and for sure can be applied way beyond
Borwein integrals.

If you liked this post, I think a great thing to see next
is the talk ["When random walkers help solving intriguing integrals"](https://youtu.be/aZ7Cyhzi9h8),
by E. Trizac. For now, thanks a lot for reading!





-------------------------------------------------------------------
[^1]:
    Keeping in mind that the $$\sinc$$ function is the
    Fourier transform of the [rectangular function](https://en.wikipedia.org/wiki/Rectangular_function)
    (and therefore of the [continuous uniform distribution](https://en.wikipedia.org/wiki/Uniform_distribution_(continuous))),
    and that products are mapped to convolutions when Fourier-transformed, you might already see
    that this problem is not so unrelated after all!