---
layout: post
title: "Ideas for Creating a Performance Culture"
date: 2020-01-12 00:00:00
categories: [performance]
---

Web performance is hard, especially on teams with varying levels of expertise and knowledge about optimization. Here are some ideas for creating a performance culture on your team:

### Focus on Targets

Get your team to focus on performance targets (e.g. scrolling at 60 FPS, 3s TTI, etc.). Break these targets down into manageable milestones (e.g improve scrolling to 40, 50, 55 FPS) and challenge your team to reach them. 

### Find ways to measure

It's important to understand whether code changes are improving performance or making it worse. Ways to measure: Chrome DevTools, React Profiler, etc. Ask your team to include before and after measurements with each pull request. This is a great way to share knowledge and celebrate improvements.

### Automate what you can measure

Manual profiling with Chrome DevTools can be tedious and time consuming. Instead, use Puppeteer and Chrome DevTools Protocol to automate test scenarios like scrolling and generate an FPS summary at the end. Summaries are easier to compare than flame charts.

Make automated performance tests part of your CI/CD pipeline and set minimum thresholds for things like Lighthouse score, TTI and FPS. Now your team has visibility into code changes that impact performance.
