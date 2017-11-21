---
layout: post
title:  "Mobile Navigation for Big Phones"
date:   2015-01-10 00:00:00
categories: [ux]
icon: 🔥
---

> As smartphones continue to get larger but our hands don’t, what kinds of design solutions can ensure mobile interactions remain comfortable, quick, and easy on our thumbs?

— [Luke Wroblewski](http://www.lukew.com/ff/entry.asp?1927)

Hold your smartphone in one hand and notice how difficult it is to reach the top of your screen. The top corners of the screen are the hardest place to reach with our thumbs, yet this is where designers continue to place navigation controls. Instead of repeating this broken design pattern, let’s give our poor thumbs a rest and consider some new possibilities for mobile navigation design.

### What does good mobile navigation look like?

- **Discoverable.** Users should be able to find the navigation quickly and understand how it works without giving it a second thought.

- **Ergonomic.** It should be easy to reach the navigation while holding the device in one hand (even on large phones). Consider how often you hold your phone in one hand (carrying groceries, holding a baby, laying in bed, etc.)


- **Unobtrusive.** The navigation shouldn’t consume excessive screen real estate and shouldn’t distract the user from the content.



### The floating action button

The [floating action button](https://material.io/guidelines/components/buttons-floating-action-button.html) was introduced in Google’s [Material Design](https://www.google.com/design/spec/material-design/introduction.html) guidelines. The button floats in the lower right corner where it can be easily reached with your thumb and tapping it reveals a set of “promoted actions”.

<video
  loop=""
  autoplay
  preload="auto"
  tabindex="0"
  class="screen-cap"
  style="max-width: 360px">
  <source
    src="{{ site.baseurl }}/images/components-buttons-fab-transition-card-02.webm"
    type="video/webm">
  <source
    src="{{ site.baseurl }}/images/components-buttons-fab-transition-card-02.mp4"
    type="video/mp4">
</video>

While the floating action button wasn’t designed for this purpose, with a few changes, it could be used to reveal the primary navigation for a wesbite. [View Codepen](https://codepen.io/peterhry/full/MYbQLN/). If you’re on a phone, notice how comfortable it is to access the menu with your thumb.

<video
  loop=""
  autoplay
  preload="auto"
  tabindex="0"
  class="screen-cap"
  style="max-width: 360px">
  <source
    src="{{ site.baseurl }}/images/big-phone-nav.mp4"
    type="video/mp4">
</video>

### The floating primary nav

The menu is split into two columns making it easy to tap all the items. In contrast, a menu that drops down from the top or slides in from the side makes it difficult to reach the topmost items. One potential drawback of this solution is that it doesn’t work well with an odd number of items.

To make the menu more discoverable, I’ve used the familiar “hamburger” icon. This combined with the fixed position over the content makes it more noticeable when scrolling.

The button might seem distracting at first but notice how it fades out after a a second to help you focus on the content. If it still bothers you, remember that your thumb is usually covering the lower right portion of the screen anyway – what’s more distracting?



### I want your feedback

Love it? Hate it? I like this solution because it solves (what I think is) a major usability issue on big phones. I’d love to get your feedback so [ping me on Twitter](https://twitter.com/peterhry) and let me know what you think.

