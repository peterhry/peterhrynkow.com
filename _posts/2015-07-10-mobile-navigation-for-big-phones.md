---
layout: post
title:  "Mobile Navigation for Big Phones"
date:   2015-01-10 00:00:00
categories: [ux]
icon: 🔥
---

> As smartphones continue to get larger but our hands don’t, what kinds of design solutions can ensure mobile interactions remain comfortable, quick, and easy on our thumbs?

— [Luke Wroblewski](http://www.lukew.com/ff/entry.asp?1927)

Navigation usability is something that tends to suffer on large phones. The top corners of the screen are the hardest to reach with our thumbs, yet this is where designers tend to position navigation controls. This made a little more sense a few years ago when the average screen size was around 3.5" but times have changed. Instead of repeating this broken pattern, let’s give our poor thumbs a rest and consider some new possibilities for mobile navigation design.

### Goals for mobile navigation

Good mobile navigation should be:

- **Discoverable.** Users should be able to find the navigation quickly and understand how it works without giving it a second thought.

- **Reachable** while holding the device in one hand (even on large phones).

- **Unobtrusive** and shouldn’t distract the user from the content.

### Why I like the floating action button

The [floating action button](https://material.io/guidelines/components/buttons-floating-action-button.html) was introduced in Google’s [Material Design](https://www.google.com/design/spec/material-design/introduction.html) guidelines. The button floats in the lower right corner where it can be easily reached with your thumb and tapping it reveals a set of “promoted actions”.

The floating action button can also be used to reveal the primary navigation for a website. [View Codepen](https://codepen.io/peterhry/full/MYbQLN/). If you’re on a phone right now, notice how comfortable it is to access the menu with your thumb.

<img
  src="{{ site.baseurl }}/images/big-phone-nav-320.gif"
  srcset="{{ site.baseurl }}/images/big-phone-nav-412.gif 1.2875x"
  class="screen-cap">


The menu is also split into two columns making it easy to tap all items. In contrast, a menu that drops down from the top or slides in from the side makes it difficult to reach the topmost items.

To help make the menu is more discoverable, I’ve used a familiar “hamburger” icon. This combined with the fixed position over the content makes it more noticeable when scrolling.

The way the button floats over the content might seem distracting at first but try it out. Notice how the button fades out after a few seconds to help you focus on the content. If that still bothers you, remember that your thumb is usually covering the lower right portion of the screen anyway – what’s more distracting?

### I want your feedback

Love it? Hate it? I like this solution because it solves (what I think is) a major usability issue on big phones. I’d love to get your feedback so [ping me on Twitter](https://twitter.com/peterhry) and let me know what you think.

