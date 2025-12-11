---
layout: post
title: "Using SVG to Shrink Your PNGs"
date: 2014-09-07 00:00:00
categories: [performance]
icon: 🔥
permalink: how-to-compress-a-png-like-a-jpeg/
---

![Sketch of masking a photo with an alpha layer]({{ site.baseurl }}/images/illustration-svg-png-shrink.svg)

Wouldn’t it be great if you could get the compression of a JPEG with the transparency of a PNG? You can, with a little layering trick I used while working on the new<a href="http://sapporobeer.ca" target="_blank"> Sapporo Beer website.</a>

![Saporro Beer]({{ site.baseurl }}/images/sapporo.jpg)

Notice how the beer can has a transparent area around the edges (there’s a video playing behind it). As a single PNG, the can graphic weighed in at over 1.2 MB. The trick below brought it down to 271 KB without giving up transparency.

First, create two files. The first is a regular JPEG without any transparency; you can compress it aggressively. The second is a tiny 8-bit PNG (alpha mask) that represents the transparent areas. The PNG is only 11 KB because it contains so few colors and no blended transparency.

![Files]({{ site.baseurl }}/images/2files1.jpg)

Next, I created a little snippet of inline SVG:

```xml
<svg viewBox="0 0 560 1388">
  <defs>
    <mask id="canTopMask">
      <image
        width="560"
        height="1388"
        xlink:href="img/can-top-alpha.png"></image>
    </mask>
  </defs>
  <image
    mask="url(#canTopMask)"
    id="canTop"
    width="560"
    height="1388"
    xlink:href="can-top.jpg"></image>
</svg>
```

Load both images inside an inline SVG. The PNG is included as a mask and applied to the JPEG to carve out the transparent area. The result: full transparency with JPEG compression.

### Caveats
1. [To work in most browsers](http://codepen.io/shshaw/full/IDbqC/) the SVG must be inline. You can’t move it into an external file and load it with an `<img>` tag.
1. No IE8 support
1. Masks don’t work in older versions of Android
