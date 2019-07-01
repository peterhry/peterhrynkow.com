---
layout: post
title: "Event Sourcing: Read Models are Overrated"
date: 2019-06-29 00:00:00
categories: [event-sourcing]
---

Schema migrations have been a persistent pain point for our team as we move toward an event-driven architecture. The operational overhead of replaying events from Kafka in order to populate read models in Elasticsearch is tedious, hard to automate, and prone to human-error.

Needless to say, I was intrigued when I came across this talk by David Schmitz (funny guy, btw) that suggests you may not need static read models in some cases. **By creating many smaller event streams, you can get good performance reading from a stream in realtime.**

<p class="video-wrapper">
  <iframe frameborder="0" scrolling="no" marginheight="0" marginwidth="0" width="788.54" height="443" type="text/html" src="https://www.youtube.com/embed/GzrZworHpIk?autoplay=0&fs=1&iv_load_policy=3&showinfo=0&rel=0&cc_load_policy=0"></iframe>
</p>