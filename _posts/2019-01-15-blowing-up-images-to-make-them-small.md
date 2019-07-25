---
layout: post
title: "How to Create Lightweight, Scalable Raster Images"
date: 2019-01-13 00:00:00
categories: [performance]
icon: 🔥
---
Raster images normally look distorted or pixelated when enlarged — especially if they contain graphics or text — but the technique I'm about to show you is all about blowing up tiny images and the results might just surprise you.

Let’s say you want to use this image as a full page background on your website:

<a href="{{ site.baseurl }}/images/1920x1080.jpg">
  <img src="{{ site.baseurl }}/images/1920x1080.jpg" />
</a>
`1920x1080.jpg (22 KB)`

Using SVG or CSS to render the image would be ideal for reducing the file size and providing resolution-independent scaling. Sadly, [neither technology supports mesh gradients](https://stackoverflow.com/questions/14926189/creating-a-gradient-mesh-in-css-jquery) at this time.

Fortunately, there's another way to achieve both a small file size and vector-like scaling using a raster image.

Here’s how it works:
1. Downsample the image so its dimensions are `32x18`
1. Export the image as a PNG. The resulting file <a href="{{ site.baseurl }}/images/32x18.png"><img src="{{ site.baseurl }}/images/32x18.png" /></a> should be around `1 KB`.
1. Use the `32x18` image as a CSS background image that covers its container:
```css
.my-background {
  width: 1920px;
  height: 1080px;
  background: url(32x18.png);
  background-size: cover;
}
```
or as an `<img>` element:
```xml
<img src="32x18.png" style="width: 100%; height: auto;" />
```

Here is the result:

<a href="{{ site.baseurl }}/images/32x18.png">
  <img src="{{ site.baseurl }}/images/32x18.png" style="width: 100%; height auto;" />
</a>
`32x18.png (1443 bytes)`

Can you tell the difference? As you can see, the stretched image looks almost identical to the original. In fact, it scales up infinitely without any loss in fidelity. Better yet,
the new image is only `1.4KB` — that's a 93.6% reduction 🎉. [Click here](https://codepen.io/peterhry/pen/maaXZX) for a side by side comparison.

Here is another example:

<a href="{{ site.baseurl }}/images/1920x1080-2.jpg">
  <img src="{{ site.baseurl }}/images/1920x1080-2.jpg" />
</a>
`1920x1080-2.jpg (143 KB)`

Notice how this image has a bit more detail than the previous one. In this case, the small image will need to be a bit larger `(128x72)` to capture the detail. Finding the right size takes some experimenting.

<a href="{{ site.baseurl }}/images/128x72.png">
  <img src="{{ site.baseurl }}/images/128x72.png" style="width: 100%; height auto;" />
</a>
`128x72.png (7 KB)`

This technique is also suitable for blurred background photos — the kind you typically see combined with a text overlay. In this case, saving the image as a jpeg yields a smaller file size.

<a href="{{ site.baseurl }}/images/1920x1080-4.jpg">
  <img src="{{ site.baseurl }}/images/1920x1080-4.jpg" />
</a>
`1920x1080-4.jpg (199 KB)`

<a href="{{ site.baseurl }}/images/256x144-4.jpg">
  <img src="{{ site.baseurl }}/images/256x144-4.jpg" style="width: 100%; height auto;" />
</a>
`256x144-4.jpg (10 KB)`

### Limitations

Before you get too excited, this technique does have one limitation. It works well for smooth gradients and images with less contrast but look what happens if you blow up this image:

<img src="{{ site.baseurl }}/images/1920x1080-3.jpg" />
`1920x1080-3.jpg (337 KB)`

<img src="{{ site.baseurl }}/images/128x72-2.png" style="width: 100%; height auto;" />
`128x72-2.png (21 KB)`

### What’s going on here?

When the downsampled image is enlarged, the browser uses an [interpolation algorithm](https://en.wikipedia.org/wiki/Image_scaling#Algorithms) to fill in the missing image data. Smooth images with less contrast can be upsampled without any distortion or pixelation because the interpolated pixels blend in with the original ones.

### How is this useful?

This technique is great for reducing the file size of background images which often contain less detail. It's also great for rendering lightweight, scalable mesh gradients — something you can't do with SVG or CSS. Mesh gradients were originally planned for SVG 2.0 but [the feature has since been removed](http://libregraphicsworld.org/blog/entry/gradient-meshes-and-hatching-to-be-removed-from-svg-2-0) from the spec.

So the next time you're about to export a big background image from Photoshop or Sketch, give this technique a try, you might be surprised how well it works.


