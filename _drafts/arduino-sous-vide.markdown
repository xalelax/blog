---
layout: post
title:  "Arduino-powered Sous-Vide Oven"
labels: [electronics, arduino, coding]
categories: Blog
---
** Disclaimer: parts of the circuit shown in
this article attach to 220V mains voltage.
Do not try this at home. **

# Introduction

For some years now %todo: quanto tempo
some innovative ways of cooking protein-rich
dishes gained quite some popularity.

Even though this rise in popularity and rise in
demand caused some inexpensive Sous-Vide devices
to hit the shelves of Supermarkets, I decided to
build my own: building stuff is fun, and in my
impossibly small apartment I already had a
(mostly unused) rice cooker which could be
recycled as a heating element. Moreover,
the extreme customizability of this device
will allow me to use it for different applications
too; by introducing custom temperature cycles,
I could use it as an incubator, or as a
yoghurt maker!


# How I built my Sous-Vide oven

The circuit I ended up building is quite simple;
the part which was challenging for me was to
understand how to regulate smoothly the power
delivered to the resistive load of the rice cooker.
I could have opted for a relay, ...
%todo: fare i conti con l'inerzia termica
I ended up overengineering things a little and
getting an ac dimmer circuit with zero cross
detection. %todo: due parole su ac dimming

In order to control temperature, I use a PID
scheme. Since I find this an extremely cool
idea which is mostly unknown by non-engineers,
let's see how it works!



# Outlook

I would like to implement a different control scheme;
in particular, instead of using a PID control with
manually-tuned parameters it would be cool if, at
runtime, a very basic AI could choose the optimal
level of power to deliver to the oven in order to
reach a set temperature.