---
layout: post
title: "Blowing Up Images to Make Them Small"
date: 2019-01-13 00:00:00
categories: [performance]
icon: 🔥
---
Normally, an image looks distorted or pixelated after being enlarged — especially if it contains graphics or text — but the technique I'm about to show you is all about blowing up tiny images and the results might surprise you.

Let’s say you want to use this image as a full page background on your website:

<img src="{{ site.baseurl }}/images/1920x1080.jpg" />
`1920x1080.jpg (22 KB)`

`22 KB` isn't too bad, but stick a few images like this on the page and things start to add up.

Here’s how you can significantly reduce the file size:
1. Downsample the image so its dimensions are `32x18`
1. Export the image as a PNG. The resulting file <img src="{{ site.baseurl }}/images/32x18.png" /> should be around `1 KB`.
1. Use the `32x18` image as a CSS background image:
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
  <img src="32x18.png" style="width: 100%; height auto;" />
```

Here is the result:

<img src="{{ site.baseurl }}/images/32x18.png" style="width: 100%; height auto;" />
`32x18.png (1443 bytes)`


Can you tell the difference? As you can see, the upsampled image looks almost identical to the original. [Click here](https://codepen.io/peterhry/pen/maaXZX) for a side by side comparison. Now the background image is only `1.4KB` — that's a 93.6% reduction 🎉.

Here is another example:

<img src="{{ site.baseurl }}/images/1920x1080-2.jpg" />
`1920x1080-2.jpg (143 KB)`

Notice how this image has a bit more detail than the previous one. In this case, the small image will need to be a bit larger `(128x72)` to capture the detail. Finding the right size takes some experimenting.

<img src="{{ site.baseurl }}/images/128x72.png" style="width: 100%; height auto;" />
`128x72.png (7 KB)`

### Limitations

Before you get too excited, this this technique does have one limitation. It works well for images with smooth gradients and few details but look what happens if you blow up this image:

<img src="{{ site.baseurl }}/images/1920x1080-3.jpg" />
`1920x1080-3.jpg (337 KB)`

<img src="{{ site.baseurl }}/images/128x72-2.png" style="width: 100%; height auto;" />
`128x72-2.png (21 KB)`

### What’s going on here?

_Disclaimer: I know very little about image scaling algorithms so please excuse my ignorance when I try to explain how this works._

When the small image is enlarged, the browser uses an [interpolation algorithm](https://en.wikipedia.org/wiki/Image_scaling#Algorithms) to fill in the missing image data. Images with few details yield better results because the interpolated pixels are closer to the original ones.

### Final thoughts

This technique is great for reducing the file size of background images which typically contain few details. It's also great for rendering lightweight mesh gradients — something you can't do with SVG or CSS.

So the next time you're about to export a big background image from Photoshop or Sketch — give this technique a try — you might be surprised how well it works.

Next up: [Using SVG to Shrink Your PNGs](https://peterhrynkow.com/how-to-compress-a-png-like-a-jpeg/)

