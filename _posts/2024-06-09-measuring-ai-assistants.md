---
layout: post
title: "Measuring AI assistants for small teams"
date: 2024-06-09 00:00:00
categories: [ai, metrics]
---

Most AI assistant pilots stall because teams cannot tell whether the tool is helping. A lightweight measurement plan makes progress visible and keeps scope creep in check.

### Start with a single job-to-be-done

Pick one repeatable task—triaging support tickets, drafting release notes, or summarizing meetings. Define what “good” looks like (speed, accuracy, tone) and measure the human baseline before the assistant joins.

### Instrument the workflow

Track how long users spend in each step and how often they accept, edit, or reject the assistant’s output. These metrics reveal whether the assistant saves time or simply shifts work into review.

### Collect qualitative feedback quickly

Short surveys after each use (“Was this helpful?” plus a free-text box) surface failure modes you would not find in logs. Rotate a small set of targeted questions weekly to avoid survey fatigue while still learning what to improve.

### Compare drafts, not just outcomes

When humans edit AI drafts, keep both versions. Analyze common edits—fixing numbers, softening tone, adding missing context—to guide prompt changes or retrieval improvements. Edits are a rich signal even when overall satisfaction is high.

### Celebrate retirements

Success is when the assistant makes a task boring. When error rates stay low and users rarely edit outputs, automate the task fully and remove it from the experimental backlog. Shipping and retiring small assistants builds confidence to tackle more ambitious workflows next.
