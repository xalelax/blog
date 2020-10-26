---
layout: post
title:  "Should employees be promoted at random?"
labels: [math, statistics]
categories: [math, statistics]
image: assets/images/peter_principle.png
---





# Pluchino-Rapisarda-Garofalo model

We shall now review the model introduced in
[The Peter Principle Revisited: A Computational Study](https://arxiv.org/pdf/0907.0455.pdf).
Let's consider a hierarchical organization, where positions are distributed over some number
of levels of fixed size; each position can be either vacant or occupied by an
employee, who is characterized by two numbers: their age and a score which
represents how competent they are in their current role. For the sake of definiteness,
let's assume that the score is a real number from 1 to 10.

![Schematic representation of a hierarchical organization](/assets/pics/peter/hierarchical_org.svg){: class="col-9"}

The global efficiency of the company is defined as the sum of the competence scores
for each employee, weighted by a level-dependent "factor of responsibility"
which accounts for the idea that incompetency or unfulfilled roles occurring in the topmost levels
are more harmful to the company as a whole with respect to them occurring in the
lower levels. The efficiency is then normalized by the score the company would
obtain if each employee had a competence score of 10.

Employees can only be promoted to the level immediately above their current one
(provided that there are vacant positions in it) and enter the company only at the
bottom-most level. How will we model then the competency an employee will show when they're
promoted to a higher level? The authors of this model suggest two possible mechanisms:
  *  **Common sense hypothesis**: when an employee is promoted, their competence in
     their new role is roughly the same they had in their previous role, with
     some small random variation (say, uniformly distributed between -1 and 1).
  *  **Peter hypothesis**: when an employee is promoted, their competence at their
     new role is completely independent with respect to the one they had at their
     previous role. Not completely unrealistic, if we imagine someone switching
     from a role where they need highly technical skills to a more managerial role.

Also, one can imagine a large number of strategies by which we choose an employee
to promote at the next level; in the original model, the authors study three of them:
  *  *The Best* strategy: for any vacant position, promote the most competent employee
     from the level immediately below. Intuitively, this is the strategy that is most
     common-sensical, and that even in the real world is most frequently used.
  *  *The Worst* strategy: rather than picking the best candidate from the level below,
     pick instead the worst one. Intuitively... WTF?
  *  *The Random* strategy: whenever there is a vacant position, write down all the
     names of the employees that can be promoted to fill it, put them into a fish bowl,
     and extract one name at random: that guy gets a promotion! Would love to know
     if anyone uses it in the real world.

Lastly, employees leave the company only if they reach retirement age[^1] or if their
competence score falls below a certain threshold.

With these rules in mind, let's implement an agent-based simulation to see how a
company, with random initial ages and competencies of employees at the start, evolves
over time. If you are not interested in the details of the implementation, feel free to skip to
the [Discusstion of the original results](#discussion-of-the-original-results)
section.

# Implementing the PRG model with Python

As this is a very simple agent-based simulation, I think a natural choice for its
implementation is the [Mesa library](https://mesa.readthedocs.io/en/master/), as
it allows us not to write a lot of boilerplate code for our simulation while at
the same time being much more easy to use with respect to some more sophisticated
library (e.g., [SimPy](https://simpy.readthedocs.io/en/latest/)). The goal of this
section is just to sketch how does the implementation of a simulation like this
looks like; for more details, feel free to take a look at the
[full repo](https://github.com/xalelax/pyter-pynciple) for this post and at the
[Mesa docs](https://mesa.readthedocs.io/en/master/).

We can start by writing a fairly uninteresting `Employee` class as
follows:

<script src="https://gist.github.com/xalelax/a3f65de48e7fb3cbef966e0583ae55b5.js"></script>

The `Employee.step()` method describes what agents do at each time step of the
simulation; in this model, it is very simple: they just get older and approach
retirement age.

The model describing the company dynamics is slightly more involved; here I am
reporting only the methods which are most important for this model:
<script src="https://gist.github.com/xalelax/13ce7e395a56b16ac4bbe30c40936681.js"></script>

As you can see, the strategies we described above for promoting employees and recalculating
competencies are essentially one-liners.

Mesa models have a very useful attribute, `data_collector`, which can keep track
of whatever observable we want during our simulations. We can make use of that,
and simulate the dynamics of a company via the following:
<script src="https://gist.github.com/xalelax/53271e90cf1f22adb5e2b17c9521fdd9.js"></script>

I.e., we just need to repeatedly call the `model.step()` method, and all the rest
is more or less taken care of automagically. Without further ado then, let's
look at the output of my implementation of the Pluchino-Rapisarda-Garofalo model,
where I used their original parameters.

# Discussion of the original results




# Extensions


# Open questions (left as an exercise for the reader)


# Conclusion






-------------------------------------------------------------------
[^1]: In the original paper, retirement age is set to 60... Between all the
      assumptions of this model, this probably is the most unrealistic one :-(
      Luckily for us right now, the specific value of this number is essentially
      unimportant for our discussion. For the future, well, that's going to be
      quite unpleasant I'm afraid.
