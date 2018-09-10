---
layout: post
title:  "Should We Design For Handedness?"
date:   2017-11-25 00:00:00
categories: [ux]
icon: 🔥
---

Lately I’ve been thinking a lot about the ergonomics of mobile devices and I wanted to know if left-handed people have more difficulty using some websites. If [80% of the world is right handed](https://www.scientificamerican.com/article/why-are-more-people-right/), could mobile interfaces be favoring right-handed users?

After doing some research, I realized that I was asking the wrong question. One [study](http://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php) found that people actually switch  how they hold their phone depending on the task, their posture, and environment. People switch between their left and right hand, and in some cases, cradle their phone in one hand while using the other to interact. In other words, **hand being used ≠ handedness**.

Later that day, I witnessed this fact while riding the bus. I was browsing twitter when I noticed that I was holding my phone in my left hand. It was definitely more difficult to use the interface. Things like navigation controls and close buttons were harder to reach.

### Can this experience be improved?

We know that responsive design is about adapting our interfaces to different screen sizes and device capabilities, but what if we took it a step further and adapted our interfaces to human factors like how the user is holding their device. Could we design a flexible interface where important controls are placed on one side of the screen or the other to improve ergonomics?

### Let’s write some code!

I wanted to see if this was possible so I began by experimenting with some JavaScript that tries to detect which hand is being used by watching how the user touches the screen. What I found was that a simple swipe gesture can reveal which hand is being used by observing the start and end position of the thumb. You can find the code on GitHub. I call it [digits.js](https://github.com/peterhry/digits.js).


### Is it worth it?

Is there potential to optimize interfaces based on which hand is being used at any given time? Maybe. But I think it gets complicated when you consider that users are switching hands throughout the day. Constantly changing the interface might just confuse people. What do you think? <a href="http://twitter.com/peterhry">Reach out to me on twitter</a> with your thoughts.
