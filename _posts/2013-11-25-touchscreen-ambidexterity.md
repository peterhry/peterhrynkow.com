---
layout: post
title:  "Touchscreen Ambidexterity"
date:   2013-11-25 00:00:00
categories: [ux]
icon: 🔥
---

Lately I’ve been thinking about the ergonomics of mobile phone interfaces. We know that roughly [80% of the world is right handed](https://www.scientificamerican.com/article/why-are-more-people-right/), but is handedness a factor in the design of mobile experiences? Do our interfaces tend to favor right-handed users? If so, how are lefties coping?

For me, responsive design is about more than adapting to different screen sizes, it’s about adapting to human factors, too. So shouldn’t our interfaces adapt to handedness?

This question led me to what I thought was a clever idea: a JavaScript library that detects handedness by analyzing how the user touches the screen. A simple swipe gesture can reveal which hand is being used by observing the start and end position of the thumb. With this information, one could design a flexible interface that adapts to the the user’s handedness. In theory, ergonomics could be improved by moving certain controls from one side of the screen to the other. I thought I was really on to something — but I wasn’t.

It turns out that handedness isn’t really the issue. One [study](http://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php) showed that people will often switch between their left and right hand depending on the task, their posture, and environment. I actually caught myself doing this while laying in bed this morning. I was laying on my right side and this made it easier to hold my phone with my left hand (I’m right-handed). The interface was reversed but somehow I was managing to get by.

I began to wonder, does our environment force us to use the opposite hand so often that we’ve gained a degree of touchscreen ambidexterity? Think of all the situations where your dominant hand is occupied with another task (holding grocery bags or the handrail on a bus) and you’re forced to hold your phone with the other hand.

Is there potential to optimize interfaces based on which hand is being used at any given time? Maybe. But I think it gets complicated when you consider that users are switching hands throughout the day. Constantly changing the interface might just confuse people.
