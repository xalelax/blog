---
layout: post
title:  "Data science job interview: easy to avoid common mistakes"
labels: [statistics, machine learning]
categories: [statistics, machine learning]
image: assets/images/doge.jpg
---


During my time at Getsafe, I had the chance to interview my fair share of candidates for data-related roles.
While doing this, I noticed something: some kinds of (simple) mistakes were overwhelmingly frequent
among candidates! In disagreement with a famous quote by Tolstoy, "All unhappy case studies look alike".

In my mind, I started painting the portrait of a "mythical" candidate that I would
hire in a heartbeat. No, not a Rockstar/Guru/Evangelist with 12 years of professional experience managing
Kubernetes clusters and working with Hadoop/Spark, while simultaneously contributing
to TensorFlow's development, obtaining 2 PhDs, and publishing at least 3 Deep Learning papers per year. Nope;
I would just instantly trust a person who convinces me on the points I am about to describe...
And I can imagine the same happening in other companies, with other interviewers.

Although this is a very personal and opinionated list, I hope these few tips and tricks
can be of some help to people at the start of their data science career. If you have more than ~6 months
of working experience, *hopefully* you will not find anything new here ¯\_(ツ)_/¯


# Sloppy use of Pandas

Let's face it: for most of your day-to-day tasks as a data scientist you will be manipulating DataFrames,
slicing them, grouping them by the values contained in a column, applying transformations to some of their
columns, and so on. Pandas IMHO is one of the most important foundational tools for a data scientist,
and if you are able to showcase some mastery with it, well, people will take you quite seriously.

On the contrary, if you systematically do very low-level manipulations on your dataframes where a built-in
Pandas command exist, you will be potentially signaling your unwillingness to read the docs
of a tool you're supposed to master, and raise all kinds of red flags.

Here are a few tricks to improve with pandas:

  1. USE IT!
  2. Whenever you have to do any manipulation of your DataFrame, stop for a couple of minutes and read the docs
     to check whether there are already built-in methods that can save you 90% of the work. Even if you don't
     find them, in the process of reading through the docs you will learn tons of stuff that quite possibly
     will come in handy in the future.
  2. Read tutorials written by trustworthy people, see how they do some operations.
     Especially, [Part II of Tom Augspurger's Modern Pandas tutorial](https://tomaugspurger.github.io/method-chaining)
     is quite a fundamental read IMHO. Even better, read not just part II, but the whole series.
	 Also, [this talk  by Vincent D. Warmerdam](https://www.youtube.com/watch?v=yXGCKqo5cEY) is worth looking at.
  3. If you have to perform some complicated, maybe not built-in, transformation of your dataframe,
     consider wrapping it in a function! After you do that, `.pipe(...)` and `.apply(...)` are your friends.

Final tip: please avoid using `inplace=True` anywhere. Contrary to what you might be thinking, it doesn't bring
any performance bonus and it naturally makes you write unclear code, as it hinders your ability to chain methods.
Hopefully this feature [will be discontinued sometime in the future](https://github.com/pandas-dev/pandas/issues/16529).

# Fitting stuff on the test set

The test set is sacred; while building and selecting a model, it should not be even looked at. 

# Flaw of averages

# Knowledge of algorithms

# Poor visualization choices

Choosing the correct options for your plots goes a long way too. Ultimately, I think the most common mistakes here
are due to poor choice of normalization or not using the correct scale for the axes. 

Let's look at an example; the following snippet of code

<script src="https://gist.github.com/xalelax/90250c17b29c20f78ae7ef097dd6b5f7.js"></script>

just creates two arrays with samples from an exponential distribution; then, it generates the following plot

![Not a really good plot](/assets/pics/itw-tips/vis1.svg){: class="col-8"}

