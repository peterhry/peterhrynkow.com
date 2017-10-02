---
layout: post
title:  "🔥 Using SVG to Shrink Your PNGs"
date:   2014-09-07 17:50:00
categories: [performance]
permalink: how-to-compress-a-png-like-a-jpeg/
---

Wouldn’t it be great if you could get the compression of a JPEG and keep the transparency of a PNG? Well, you can, sort of. Here’s a little trick that I discovered while working on the new<a href="http://sapporobeer.ca" target="_blank"> Sapporo Beer website.</a>

![Saporro Beer](http://peterhrynkow.com/wp-content/uploads/2014/09/sapporo.jpg)

Notice how the beer can on the Sapporo website has a transparent area (it’s hard to notice but there’s a video playing behind it). As a PNG, the beer can graphic would have been over 1.2 MB! So how did I get it down to 271KB?

First, I created two files. The first is a regular JPEG without any transparency. You can compress this one as much as you like. The second is an 8-bit PNG (alpha mask). This is just a black and white image that represents the transparent areas of the beer can. Notice how the PNG is only 11KB; that’s because it contains so few colors and no transparency.

![Files](http://peterhrynkow.com/wp-content/uploads/2014/09/2files1.jpg)

Next, I created a little snippet of inline SVG:

```xml
<svg preserveAspectRatio="xMinYMin" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 560 1388">
  <defs>
    <mask id="canTopMask">
      <image width="560" height="1388" xlink:href="img/can-top-alpha.png"></image>
    </mask>
  </defs>
  <image mask="url(#canTopMask)" id="canTop" width="560" height="1388" xlink:href="can-top.jpg"></image>
</svg>
```

Here I am loading the two images inside an SVG container. The alpha channel image is included as a mask which is then applied to the JPEG to create the transparent region. <span style="line-height: 1.5em;">The result? A transparent image with JPEG compression!</span>
## Caveats
- [To work in most browsers](http://codepen.io/shshaw/full/IDbqC/) the SVG must be inline. You can’t move it into an external file and load it with an `<img>` tag.
- No IE8 support
- Masks don’t work in older versions of Android
