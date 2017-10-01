---
layout: post
title:  "Configuring BackstopJS to run in Travis"
date:   2017-09-29 17:50:00
categories: [testing]
---

If you see that message in your travis log, you’ll need to load the chrome beta addon and set `CHROME_PATH` so chrome-launcher knows where to find the beta.

```yml
addons:
  chrome: beta
env:
  - CHROME_PATH=/usr/bin/google-chrome-beta
```
