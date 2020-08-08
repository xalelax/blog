---
layout: post
title:  "Data science case studies: easy to avoid common mistakes"
labels: [statistics, blog]
categories: [statistics, blog]
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

Although this is a personal and quite opinionated list, I hope these few tips and tricks
can be of some help to people at the start of their data science career!
I am putting here only the more DS-related things that came to my mind, but of course writing Pythonic, 
readable, and expressive code is also something that will please immensely whomever is interviewing you!

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

# Information leaking from the test set

The test set is sacred; while building models or selecting the best one, it should not be even looked at. 
Think about it: the reason why we have a test set in the first place is that we want to have an estimate
of the generalization error of our model which is as unbiased as possible. If we are allowed to get a 
sneak peek into "the future" (i.e., data that during training and model building fundamentally we should not 
have access to) it's almost guaranteed that we will get influenced by that, and bias our error estimates.

Although I've never seen anybody *directly* fit a model on the test set, quite commonly instead candidates
performed hyperparameter tuning and model selection by looking at some metric on the test set. Save part
of the data as a validation set instead, or even better, perform cross-validation.

Another quite common thing which causes leakage of information from the test set is fitting scalers
(like `sklearn.preprocessing.StandardScaler`) or oversampling routines 
(e.g., `imblearn.over_sampling.SMOTE`) on the whole dataset. Again, feature engineering, resampling, and
so on are part of how a model is built and trained: keep the test set out of it.

# Flaw of averages

Although summary statistics, like averages, quantiles, and so on, are useful to get a first impression
of the data, don't make the mistake of reducing distributions to a single number when this doesn't
make sense. A classic cautionary example to showcase this is 
[Anscombe's quartet](https://en.wikipedia.org/wiki/Anscombe%27s_quartet), but my favorite is 
the [Datasaurus Dozen](https://www.autodeskresearch.com/publications/samestats). 

![Source: Autodesk Research](https://d2f99xq7vri1nk.cloudfront.net/DinoSequentialSmaller.gif){: class="col-8"}

More often than not, the distribution of your data points matters more than their average value, 
and especially in some applications the shape of the tails of your distributions is what at 
the end of the day governs decisions.

# Knowledge of algorithms

When you are given a case study, you often have an advantage you can capitalize on: you choose the kinds
of models to use. That means that you can anticipate some of the questions interviewers might ask you.

For example, if you end up using an `XGBClassifier` for your task, try to understand how it works, 
as deeply as you can. Everyone knows it's based on decision trees, but which other "ingredients" 
do you need for it? Do you know how XGBoost handles missing values? 
Could you explain Bagging and Boosting in layman's terms?

Even if you end up using linear regression, you should have a clear idea about what is happening under
the hood.

# Poor visualization choices

Choosing the correct options for your plots goes a long way too. Ultimately, I think the most common mistakes here
are due to poor choice of normalization or not using the correct scale for the axes. 

Let's look at an example; the following snippet of code

<script src="https://gist.github.com/xalelax/90250c17b29c20f78ae7ef097dd6b5f7.js"></script>

just creates two arrays with samples from an exponential distribution; then, it generates the following plot

![Not a really good plot](/assets/pics/itw-tips/vis1.svg){: class="col-8"}

I saw some variation of this an enormous amount of times; basically, what we would really like to do is
compare the distribution of something among two groups, but in this plot we are only showing raw counts.
If one of the groups has more samples than the other, a plot like the one above is meaningless to get an
idea of the underlying distributions. A better choice would be to normalize what we are displaying in a 
sensible way: in this case, just setting the parameter `density=True` transforms the raw counts into
relative frequencies, and gives us the following:

<script src="https://gist.github.com/xalelax/903f478d3135a8ba14c3e75c3973f12a.js"></script>
![Better histogram for the comparison of distribution](/assets/pics/itw-tips/vis2.svg){: class="col-8"}

Nice! Now we can explicitly see that, after all, `a` and `b` are samples from the same distribution.
There is still something that I dislike here: a lot of white space, and the fact that for values of `a`
or `b` larger than 4, I cannot really see any bar clearly. Luckily, since 1614 Logarithms are a common 
mathematical operation... So common that we even have a dedicated keyword argument in `plt.hist(...)` that 
just transforms our linear y-axis to a logarithmic one:

<script src="https://gist.github.com/xalelax/8880f06bda8cfae0d567eb2accc6b8c8.js"></script>
![Are we there yet?](/assets/pics/itw-tips/vis3.svg){: class="col-8"}

Notice that this is by no means a "perfect" plot: our axes are unlabeled, no legend, and it just looks kinda
ugly! But at least, we can extract insights that we would have never been able to see with 
just a call to `plt.hist([a,b])`.

# Conclusion

What all the above-listed mistakes have in common is that they are easily avoidable with some
thought and knowledge of the subject, so my advice for your next data science case study is: 
relax, focus, try to be one step ahead of whatever mind game they're playing with you, 
and Google for stuff (a lot!).

To conclude, I wish you all the best in your career, whatever job you happen to be doing now!
Maybe see you at an interview :-P
