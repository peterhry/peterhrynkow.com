---
layout: post
title:  "Hacking Image Interpolation for Fun and Profit"
date:   2019-01-13 00:00:00
categories: [performance]
icon: 🔥
---

If you’ve ever wanted to create a mesh gradient on the web, you were probably disappointed to find out that there is no programmatic way to create them.

At the time of writing, CSS does not support mesh gradients nor does SVG. Support for mesh gradients was originally planned for SVG 2.0 but the feature has since been removed from the spec.

Some workarounds include:

1. Layering CSS radial gradients
1. Using a heavy CSS blur filter to blend coloured shapes
1. Raster images (jpeg, png, etc.)

Recently, I discovered another solution that is lightweight and produces great results.

Normally, taking a small image and enlarging it looks terrible — especially if it contains a graphic, logo, or text — but the technique I'm going to show you does just that and the results might just surprise you.

Here’s how it works:

1. Generate a mesh gradient using a using a tool like Illustrator. Here is an example of one I created: <img src="{{ site.baseurl }}/images/1920x1080.jpg" />
1920x1080.jpg (23Kb)
1. Downsample the image so its width is 16px
1. Export the image as a PNG:
<img src="{{ site.baseurl }}/images/16x9.png" />
The resulting file should come in under 1Kb.
1. Use the 16x9 image as a CSS background image, like so:
```css
.gradient-mesh {
  width: 1920px;
  height: 1080px;
  background: url(16x9.png);
  background-size: cover;
}
```
The browser stretches the tiny image to fill the container using its interpolation algorithm. The result looks surprisingly like the original full size image yet comes in under 1Kb 🎉.

Here is another example:

Original image 1920x1080-2.jpg (23Kb):
<img src="{{ site.baseurl }}/images/1920x1080-2.jpg" style="height: 512px; width: 100%;" />

Downsampled image 16x9-2.png (658 bytes):
<img src="{{ site.baseurl }}/images/16x9-2.png" style="height: 512px; width: 100%;" />

### Animation

<!--
This leaves you with a few options, all of which have drawbacks:

1. A large bitmap image
1. Render some shapes with CSS then apply a heavy CSS blur filter to smooth everything out. The problem with this appraoch is that the blur filter is very expensize performance-wise. The other problem is that the edges of the container get blurred which is isn’t ideal.
1. Stacking CSS radial gradients to emulate the appearance of a mesh gradient. This process is quite tedious and doesn’t look quite as good.

There’s another option that I discovered today and I think it’s pretty cool.
-->
