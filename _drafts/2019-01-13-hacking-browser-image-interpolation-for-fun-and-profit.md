---
layout: post
title:  "Hacking Image Interpolation for Fun and Profit"
date:   2019-01-13 00:00:00
categories: [performance]
icon: 🔥
---

If you’ve ever wanted to use a mesh gradient on the web, you were probably disapointed to find out that it’s not so easy.

At the time of writing, CSS does support mesh gradients nor does SVG. Support for mesh gradients was originally planned for SVG 2.0 but the feature has since been removed from the roadmap.

Some solutions include, using a large raster image, using CSS blur filter to smooth

Normally, taking a small image and enlarging it looks terrible — especially for graphics like logos, text, etc. — but this technique does just that and the results might surprise you.

Here’s how it works:

1. Generate a mesh gradient using a using a tool like Illustrator. Here is an example of one I created: <img src="{{ site.baseurl }}/images/1920x1080.jpg" />
1920x1080.jpg (23Kb)
1. Export the image as a PNG, downsampling it to 16px wide:
<img src="{{ site.baseurl }}/images/16x16.png" />
The resulting file should come in under 1K 🎉.
1. Use the 16x9 image as a CSS background image:
```css
.gradient-mesh {
  background: url(16x9.png);
  background-size: cover;
}
```
The browser will stretch the tiny image to fill the container. When the image is stretched, the browser will smooth out the pixels using its image interpoltation algorithm.


The result might surprise you.


Original image 1920x1080-2.jpg (23Kb):
<img src="{{ site.baseurl }}/images/1920x1080-2.jpg" style="height: 512px; width: 100%;" />

Downsampled image 16x9-2.png (658 bytes):
<img src="{{ site.baseurl }}/images/16x9-2.png" style="height: 512px; width: 100%;" />


<!--
This leaves you with a few options, all of which have drawbacks:

1. A large bitmap image
1. Render some shapes with CSS then apply a heavy CSS blur filter to smooth everything out. The problem with this appraoch is that the blur filter is very expensize performance-wise. The other problem is that the edges of the container get blurred which is isn’t ideal.
1. Stacking CSS radial gradients to emulate the appearance of a mesh gradient. This process is quite tedious and doesn’t look quite as good.

There’s another option that I discovered today and I think it’s pretty cool.
-->
