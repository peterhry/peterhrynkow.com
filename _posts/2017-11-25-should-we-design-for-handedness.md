---
layout: post
title:  "Should We Design For Handedness?"
date:   2017-11-25 00:00:00
categories: [ux]
icon: 🔥
---

Lately I’ve been thinking a lot about the ergonomics of mobile phone interfaces and I got wondering if left-handed people have more difficulty using some websites. If [80% of the world is right handed](https://www.scientificamerican.com/article/why-are-more-people-right/), should handedness be a consideration when designing our mobile experiences?

We know that responsive design is about adapting our interfaces to different screen sizes and device capabilities, but what if we took it a step further and adapted them to human factors like handedness. Could we design a flexible interface where important controls are placed on one side of the screen or the other depending on the user’s handedness?

### Let’s write some code!

I began by experimenting with some JavaScript that detects handedness by watching how the user touches the screen. It turns out, a simple swipe gesture can reveal which hand is being used by observing the start and end position of the thumb. You can find the code on GitHub.


### Handedness ≠ Hand being used

It turned out, I was getting ahead of myself. One [study](http://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php) found that people will switch between their left and right hand depending on the task, their posture, and environment. Think of all the situations where your dominant hand is occupied with another task (holding grocery bags or the handrail on a bus) and you’re forced to hold your phone with your non-dominant hand.

I actually noticed myself doing this today. I was laying on the couch on my right side which made it difficult to hold my phone with my dominant hand. It was definitely more difficult to use the interface with my left hand and I wondered if there was an opportunity to improve this experience.

### Is it worth it?

Is there potential to optimize interfaces based on which hand is being used at any given time? Maybe. But I think it gets complicated when you consider that users are switching hands throughout the day. Constantly changing the interface might just confuse people. What do you think? <a href="http://twitter.com/peterhry">Reach out to me on twitter</a> with your thoughts.
