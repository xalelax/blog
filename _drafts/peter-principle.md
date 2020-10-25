---
layout: post
title:  "Should employees be promoted at random?"
labels: [math, statistics]
categories: [math, statistics]
image: assets/images/borwein.png
---

# Introduction

# Pluchino-Rapisarda-Garofalo model

In this section I will provide a review of the model introduced in
[The Peter Principle Revisited: A Computational Study](https://arxiv.org/pdf/0907.0455.pdf).
Let's consider a hierarchical organization, where positions are distributed over some number
of layers of fixed size; each position can be either vacant or occupied by an
employee, who is characterized by two numbers: their age and a score which
represents how competent they are in their current role. For the sake of definiteness,
let's assume that the score is a real number from 1 to 10.

The global efficiency of the company is defined as the sum of the competencies
of each employee, weighted by a level-dependent "factor of responsibility"
which accounts for the idea that incompetency or unfulfilled roles occurring in the topmost levels
are more harmful to the company as a whole with respect to them occurring in the
lower levels.

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

# Implementing the PRG model with Python


# Extensions


# Open questions (left as an exercise for the reader)


# Conclusion






-------------------------------------------------------------------
[^1]: In the original paper, retirement age is set to 60... Between all the
      assumptions of this model, this probably is the most unrealistic one :-(
      Luckily for us right now, the specific value of this number is essentially
      unimportant for our discussion. For the future, well, that's going to be
      quite unpleasant I'm afraid.
