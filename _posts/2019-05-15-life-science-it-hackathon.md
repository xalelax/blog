---
layout: post
title:  "{Life Science} meets IT Hackathon, or how we built a wearable in a weekend"
labels: [IoT, wearables, blog]
categories: [IoT, blog]
image: /assets/images/glove.jpg
mathjax: false
---

Last weekend I got the chance to participate to 
the [{Life Science} meets IT Hackathon](http://www.lifesciencemeetsit.eu/),
and it was really great! I met so many interesting
people, been advised from awesome mentors, and
got to play a bit with IoT devices![^1]

Together with Marlene, [Kishan](https://www.linkedin.com/in/kishanthodkar/), 
and [Nils](https://www.linkedin.com/in/nilsstotz/), we designed
a wearable for health/sleep tracking 
which updated measurements from its sensors to 
the cloud. For the first time in my life I could play with 
IoT devices and IBM Watson; we were able to connect the glove
via Bluetooth to a smartphone, and from there the data collected
by the sensors in the glove was sent to the cloud. Nothing
extremely complicated, but there is a unique feeling 
when so many parts speak to each other without failing[^2]!

I also designed an extremely simple dashboard which shows
the collected data in real time! I did it with
[Node-RED](https://nodered.org/), but it
gave me the motivation to finally delve into 
front-end development.

The brain of our project was this beauty: 

![SIMBA-PRO vs my hand](/assets/pics/lifeit/simba.jpg){: class="col-7"}

i.e., the SIMBA-PRO from SensiEDGE. In a circuit smaller than an SD 
card it includes a low-power ARM 32-bit Cortex-M4 CPU with loads and
loads of sensors and a Bluetooth antenna! Technology has come so 
far! I definitely need to buy one for personal projects.

In the end, it was a wonderful experience, and I was amazed by the ideas
I heard during this event, and I am sure some of them really
have some potential to help patients and the health industry.

Unfortunately we did not win any prize as a team, but at a raffle
I won this beautiful ESP32 which I will use to upgrade my
sous-vide oven (a rice cooker currently powered by an Arduino Uno) 
to the IoT!

![ESP32 vs my hand](/assets/pics/lifeit/esp32.jpg){: class="col-7"}

...But more on that later ;-)

--------------------------

[^1]:
    There was also a lot of stuff to eat/drink, and the fact
    it is not the first thing I mention says a lot about how cool 
    were the other things!

[^2]:
    Except, of course, during the final live tech demo. Should I
    start believing in Murphy's law?