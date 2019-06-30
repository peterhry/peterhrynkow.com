---
layout: post
title: "Event Sourcing - You're Doing it Wrong"
date: 2019-06-29 00:00:00
categories: [event-sourcing]
---

This is a great talk on Event Sourcing. The key takeaway for me: By creating many smaller event streams, you can get good performance reading a stream in realtime (i.e. no need for static read models). The advantage is consistent reads and less operational overhead (no need for replays, re-indexing, etc.)

<div class="video-wrapper">
  <iframe frameborder="0" scrolling="no" marginheight="0" marginwidth="0" width="788.54" height="443" type="text/html" src="https://www.youtube.com/embed/GzrZworHpIk?autoplay=0&fs=1&iv_load_policy=3&showinfo=0&rel=0&cc_load_policy=0"></iframe>
</div>