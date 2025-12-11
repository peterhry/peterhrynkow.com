---
layout: post
title: "Event Sourcing: Read Models are Overrated"
date: 2019-06-29 00:00:00
categories: [event-sourcing]
---

![Sketch of event streams without heavy read models]({{ site.baseurl }}/images/illustration-event-sourcing.svg)

Schema migrations have been a persistent pain point for our team as we move toward an event-driven architecture. Replaying events from Kafka to populate read models in Elasticsearch is tedious, hard to automate, and prone to human error.

That’s why this talk by David Schmitz caught my attention. The idea: **you may not need static read models in some cases. By creating many smaller event streams (e.g. one per entity), you can get good performance replaying events in real time.**

Instead of baking assumptions into a single read model, let consumers project the events they need on demand. With smaller, targeted streams, replay becomes fast enough to serve fresh views without a migration marathon.

<p class="video-wrapper">
  <iframe frameborder="0" scrolling="no" marginheight="0" marginwidth="0" width="788.54" height="443" type="text/html" src="https://www.youtube.com/embed/GzrZworHpIk?autoplay=0&fs=1&iv_load_policy=3&showinfo=0&rel=0&cc_load_policy=0"></iframe>
</p>
