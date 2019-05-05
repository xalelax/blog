---
layout: post
title:  "Bayesian Inference and the Supernatural"
labels: [statistics, philosophy]
mathjax: true
categories: Blog
image: assets/images/ghost.jpg
---

Fun fact: Bayes' Theorem,
arguably one of the most important theorems in statistics,
was discovered by Reverend Thomas Bayes, a Presbyterian minister/mathematician/theologian,
and it was published posthumously thanks to Richard Price, a mathematician and Congregational minister.
It should not come as a surprise, then, that it can be
applied to questions at the fringe of Science and bear
interesting results.

In particular, in this post I wanted to provide a simple introduction
to Bayes' Theorem and Bayesian Inference, and show some applications to
supernatural phenomena; this weird choice of topic is motivated both
by the alleged motivations behind the discovery of Bayes' Theorem
and by the fact that supernatural phenomena and theological problems
are for a lot of people much more captivating than
dice throws or other textbook examples. ¯\\_(ツ)_/¯ 

I will start by writing some basic facts about probabilities and Bayes' theorem;
very likely the more experienced readers will be familiar with the content of the first sections,
therefore I invite them to skip directly to the section
[Probing the Paranormal with Experiments](#paranormal)

Full disclosure, since for many people this is a delicate argument: **I am not religious** 
and I **do not** believe in anything supernatural. **I will try to avoid strawmanning 
positions different from mine**, but I apologize in advance if I do, and please let me know
what I got wrong by contacting me or in the comment section.

# Probabilities and Experiments

Let's start from an apparently elementary concept: the definition of probabilities.
One way to define the probability of an event is to identify it as the limit of
the relative frequency of that event with respect to every possible outcome, when the 
number of "experiments" tends to infinity.

For instance, if a coin is thrown $$100$$ times and it lands $$57$$ times on heads,
the measured relative frequency of heads is $$57/100$$. In order to obtain the **probabilities**
associated with the throw of that coin, we need to calculate the limit of the relative
frequency of heads in $$N$$ throws as $$N$$ grows infinitely. For a "fair"
coin tossed in a "fair" way the probability of
heads is, by definition,  $$P_H = 1/2$$  [^1].

This procedure makes sense mathematically,
but it leaves us with a problem: in order to know the probability of an outcome, we would
need to perform an infinite amount of experiments. Since at the end of the day
one can perform only a finite amount of experiments, people adopting this definition of probabilities,
the so-called "frequentist interpretation", 
mostly work with objects such as estimators and confidence intervals
to extract as much information as possible from the finite number of samples one can obtain 
from experimental data. In the frequentist approach, we could then give an estimate of 
the probability of obtaining heads after throwing a certain coin and how confident we are
that the __true__ probability lies in a certain interval.

Aside of the frequentist interpretation, there is another view of statistics, held by many
scientists, which is called Bayesian interpretation. In order to understand it, we need to know
some things about Bayes' Theorem.

# Bayes' Theorem and Bayesian Inference

The probability of an outcome $A$ assuming another event $B$ happened
is called a [conditional probability](https://en.wikipedia.org/wiki/Conditional_probability),
and is typically written as $P(A|B)$. For definiteness, let's consider a fair die; we know
that the probability of throwing it and getting a 1 is $$P(\text{Die throw} = 1) = 1/6$$. If
a friend throws the same die and tells us only that they obtain an odd number,
in order to calculate the probability that they got a 1 without discarding the information they gave us
we would use the conditional probability $$P(\text{Die throw} = 1 | \text{Die throw is odd})$$.

Conditional probabilities are defined as: 

$$
P(A|B) = \frac{P(A \text{ and } B)}{P(B)};
$$

thus, for the example I just gave,  $$P(\text{Die throw} = 1 | \text{Die throw is odd}) = 1/3$$, 
as it could be expected intuitively.

In very few algebraic steps, it's possible to derive Bayes' theorem from the definition of 
conditional probabilities given above, and it reads

$$
P(A|B) = \frac{P(B|A) P(A)}{P(B)};
$$

_Tip for the proof: start with the definitions of $$P(A|B)$$ and $$P(B|A)$$, and 
notice that $$P(A \text{ and } B) = P(B \text{ and } A)$$_

This innocuous-looking statement about conditional probabilities can become very suggestive if 
we are dealing with the problem of having a hypothesis $$H$$ and some data $$D$$ and we are interested 
in how much the data $$D$$ supports or refutes the hypothesis $$H$$ (this kind of problem is 
typically called hypothesis testing). It can be that the probability that $$H$$ is true, given
the observed data $$D$$, cannot be easily computed directly. By using Bayes' Theorem 
we can write it as

$$
P(H \text{ is true}| D \text{ observed}) = \frac{P(D \text{ observed} | H \text{ is true}) 
P(H \text{ is true})}{P(D \text{ observed})}.
$$

We have quite a few factors here; let's analyze them one by one:
 *  $$\frac{P(D \text{ observed} | H \text{ is true})$$ is called the **likelihood**,
    and it is the probability of observing $$D$$ if $$H$$ is true. If our 
    hypothesis $$H$$ allows us to make predictions on the outcomes of experiments
    (as any quantitative model of a phenomenon should be able to do),
    this quantity is directly computable. Notice that if the likelihood is 
    close to one it just means that the outcomes D can be obtained very likely
    from the hypothesis H; this does not tell us that our hypothesis is likely correct
    (there could be radically different hypotheses for which the likelihood is 
    also close to one). Conversely, if the likelihood is very close to zero,
    we could indeed be justified in rejecting H because of its disagreement 
    with the observed data.
 *  $$P(D \text{ observed})$$ could be very difficult to calculate, but notice that it does not depend 
    on our particular hypothesis, thus if we are testing multiple hypotheses against 
    $$D$$ this factor can be **ignored**. 
 *  $$P(H \text{ is true})$$ is called the **prior** probability, and it is the 
    thing which confused me the most when I first approached Bayesian Inference.
    How are we supposed to know how likely a hypothesis is true _without
    looking at $$D$$_?

In order to shed some light on the dilemma of prior probabilities, let's think for 
a moment about how humans become convinced of things.

If two friends meet and decide to play cards together, it would be (in my experience)
quite unusual that one of them wants to check the deck before playing, look for 
marks in each card, analyze the shuffling procedure they adopt in order to be 
sure that it ensures randomness and perform other statistical tests. Why is that?
There are of course matters of practicality: the above-mentioned procedures steal 
a lot of time from the two friends, time which could be spent doing something 
they would enjoy more (i.e., playing!). Most importantly: they are friends, so they
trust each other! In "Bayesian" terms, for each of them the hypothesis 
$$H = \text{"My friend wants to trick me"}$$ is most likely false
(i.e., $$P(H) \approx 0$$), even if they never played cards together before.

Another example: most physicists in the 19th century were likely convinced that
classical mechanics could explain every measurable phenomenon; we can imagine that
their prior belief regarding the probability that classical mechanics was correct 
everywhere was such that $$P(\text{Classical Mechanics}) \approx 1$$.
This belief was changed [^2] by the experiments and the theoretical advances
which led to Quantum Mechanics and Relativity.

Thus, although it sounds strange at first, for many situations the sets
of beliefs which each human holds (with perhaps varying confidence) 
can be translated into prior probabilities. Even if we are maximally ignorant
about something we could formulate a set of hypotheses and assign to each of them
the same prior probability. 

The main idea behind **Bayesian Inference** is to update prior probabilities with
evidence, and the recipe to do so is Bayes' theorem: after looking at the data, my 
prior belief $$P(H)$$ will be substituted by $$P(H|D)$$, and this process can be repeated 
iteratively as I will see new data. I could be convinced that

$$
P(\text{The sun rises every morning}) = 
P(\text{The sun doesn't rise every morning}) = 1/2,
$$

but as each morning I observe the sun rising, $$P(\text{The sun rises every morning})$$
will become close to $$1$$, whereas $$P(\text{The sun doesn't rise every morning}) = 1/2$$
becomes smaller and smaller.

Notice that if for a certain hypothesis we hold the prior $$P(H) = 0$$, that is,
we think $$H$$ is physically impossible, in no way can we change our minds via Bayesian 
Inference (this is immediate from the form of Bayes' Theorem). Therefore, it is often
useful to have "an open mind" and assign to implausible beliefs small _but nonzero_ 
prior probabilities; perhaps the data will make them even more implausible.


# Probing the Paranormal with Experiments <a name="paranormal"></a>

How does all this relate to miracles and the supernatural? A lot of historians speculate
that Bayes and Price, both christians (thus, in particular, they believed in the 
miracle of the resurrection of Jesus), were assessing claims made by the greatest skeptic of their
time, David Hume.

In particular, in Section X of _An Enquiry concerning Human Understanding_, Hume writes:
>"No testimony is sufficient to establish a miracle, unless the testimony be of such 
> a kind, that its falsehood would be more miraculous than the fact which it 
> endeavors to establish."
If we consider the resurrection of Jesus, we should ask ourselves
whether it is more likely that the reports we have of it are false with respect to 
the possibility that someone actually came back from the dead. Since we have many
examples of people lying or making up stories, and very few, if any, examples of people
resurrecting, according to Hume we cannot accept as evidence the testimony left to us 
regarding the resurrection.

Not being able to prove a miracle happened, however, is not the same as refuting it.
Bayes' theorem can be used to show that no number of negative observations (for instance,
people dying and not resurrecting) can completely rule out a miracle on statistical grounds.
If we did not rule out a priori the possibility of a miracle by using the prior 
$$P(\text{Miracle}) = 0$$, no finite amount of data can
render $$P(\text{Miracle} | \text{Observations})$$ equal to zero. 




# Conclusion

There is something rather remarkable in the way Bayesian inference ... and 
the way people reach their conclusions. In this post I presented only a few things,
but there are even more complex human behaviors (....) which can be beautifully
interpreted in a Bayesian fashion. Many (...) argued that Bayesian inference
is ... scientific method. Indeed, scientists more often than not build their set of
beliefs not really by performing experiments themselves

Conversely, regarding ordinary events, if somebody told me "I have a dog" he would be
reporting something not particularly rare; depending on how much I trust that person
(and the consequences that me believing him have) I could accept that claim without 
the need of further evidence. Of course, I think that most people would find the 
much more unlikely claim "I have a million dogs" difficult to believe by itself, 
even if it comes from a very close and trusted friend.

[^1]:
    When dealing with real coins thrown in the real world, things are much more
    complicated with respect to ideal fair coins. For an introduction, see
    ["How random is a coin toss? - Numberphile"](https://www.youtube.com/watch?v=AYnJv68T3MM)
    and the work of the legendary [Persi Diaconis](https://en.wikipedia.org/wiki/Persi_Diaconis]).

[^2]:
    This is of course not to be taken literally; humans are complex and (quite often) stubborn. 
    A lot of physicists which studied Classical Mechanics for most of their lives 
    were never convinced in the correctness of Quantum Mechanics. Still, the beliefs 
    of the scientific community change, also because as older scientists retire younger ones 
    (hopefully with more malleable sets of beliefs) take their place.