---
layout: post
title:  "Should We Design For Handedness?"
date:   2017-11-25 00:00:00
categories: [ux]
icon: 🔥
---

Lately I’ve been thinking a lot about the ergonomics of mobile devices and I wanted to know if left-handed people have more difficulty using some websites. I wondered, if [80% of the world is right handed](https://www.scientificamerican.com/article/why-are-more-people-right/), could the design of mobile interfaces be favoring right-handed users?

After doing some research, I realized that I was asking the wrong question. One [study](http://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php) found that people switch how they hold their phone depending on the task, their posture, and environment. For example, a user holding grocery bags in their dominant hand might hold their phone with their non-dominant hand. Similarly, a user laying on a couch might cradle their phone in one hand while interacting with the other. So perhaps a better question to ask, is whether the design of a mobile interface favors one way of holding a device.

Later that day, I was browsing twitter when I noticed that I was holding my phone in my left hand. I decided to pay special-attention to the user experience. Some things like scrolling were easy but reaching the top-right corner of the screen was almost impossible without adjusting my grip. Had the controls been placed on the left side of the screen, I might have had an easier time.

### Can this experience be improved?

We know that responsive design is about adapting our interfaces to different screen sizes and device capabilities, but what if we took it a step further and adapted our interfaces to human factors like how the user is holding their device. Could we design a flexible interface where important controls move from one side of the screen to the other to improve ergonomics?

### Let’s write some code!

First I wanted to see if it was even possible to detect how a user is holding their device, so I started playing with some JavaScript. What I found was that a swipe gesture can reveal which thumb is touching the screen by observing the start and end position of the swipe. You can [find the code on GitHub](https://github.com/peterhry/digits.js). One thing to note is that the algorithm works well when the user is holding their device in one hand but gets confused during two-handed use.

The script could perhaps be improved by incorporating data from the [`DeviceOrientation`](https://developer.mozilla.org/en-US/docs/Web/Events/deviceorientation) API. Knowing the angle the device is held at might offer clues as to how the user is holding the device. For example, if the screen is angled downward, the user might be laying-down with the phone held above their head.

### Is it worth it?

Is there potential to optimize interfaces based on how the user is holding their device? Maybe. But I think it gets complicated when you consider how often people change their Constantly changing the interface might just confuse people. What do you think? <a href="http://twitter.com/peterhry">Reach out to me on twitter</a> with your thoughts.
