---
layout: post
title: "Adaptive content everywhere"
date: 2013-02-06 00:00:00
categories: [content]
---

![Sketch of adaptive content flowing to multiple devices]({{ site.baseurl }}/images/illustration-adaptive-content.svg)

What do we do when content created for a desktop website needs to fit on a smartphone screen? The temptation might be to create a second set of articles just for phones. But what about tablets, laptops, and all the devices in between? Maintaining versions for each form factor quickly becomes impossible, so we need a better approach.

At An Event Apart in San Francisco, a [presentation by Karen McGrane](http://aneventapart.com/news/post/aea-video-karen-mcgrane-adapting-ourselves-to-adaptive-content) reinforced this idea: we should create content once and publish it everywhere.

McGrane calls it adaptive content. Instead of thinking in pages, we think in reusable chunks that are richly described with metadata.

> “Metadata is the new art direction”

[@studip101](https://twitter.com/studip101)

Metadata describes the structure of content: what matters, when it happened, where it happened, and what it’s related to. For a blog post, that might include the author, date, tags, related media, and even the allowed summary lengths. All of this describes the content without dictating its appearance, letting the same material be presented in a variety of layouts. Consumer apps and websites can rely on the metadata to make smart decisions about hierarchy and placement.

McGrane also suggests writing content with reuse in mind. It’s not just about devices; the same article may appear on another page, partner sites, or social networks. Instead of truncating text in arbitrary ways, offer purposeful versions: a tweet-sized hook, a short excerpt for sidebars, and a full version for detail pages. Those deliberate cuts make the piece clearer in every context.

> “Truncation is not a content strat...”

[@karenmcgrane](https://twitter.com/karenmcgrane)

The qualities that make content adaptive also make it distributable and easy to work with. Without writing much code, developers can fetch content from an API and render it differently per platform. NPR (National Public Radio) saw an 80% increase in page views after launching their content API, and many of their apps were built in just a few weeks because the content arrived ready to use.

Creating content once and publishing it everywhere reduces overhead and ensures that every device—and every channel—gets a coherent, intentional experience.
