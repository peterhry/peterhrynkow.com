---
layout: post
title: "Why You Should Use a JavaScript Framework"
date: 2019-07-13 00:00:00
categories: [programming]
---

Recently I was asked why using a framework like React, Angular and Vue.js is better than Vanilla JavaScript.

Lately I've noticed some folks on Twitter who insist that developers learn Vanilla JavaScript before using a framework. Some go as far as saying that JavaScript frameworks are unnecessary and that Vanilla JavaScript is all you need to build a web application these days. 

The backlash against frameworks and libraries for web development is in some ways understandable.

That may be true in some cases. Using React to build a personal blog might be overkill, then again, I've seen some amazing stuff built with Gatsby.

I get it, 

For some projects, a framework is overkill. I wouldn't recommend Angular for a blog.


> I don't bother with frameworks, I just use vanilla JavaScript.

> Roughly translated:

> I'm smarter than the thousands of people who tried to solve the problems I'm about to solve. I’m an expert on security, a11y, browser support, and performance. I don't care about ROI, I just want to code.

I wanted to follow this up with a blog post because it's hard to provide context in 240 characters.

First a bit of context, I'm using the term "framework" pretty loosely here. I'm referring to proper frameworks like Ember, Angular, and Vue.js, as well as libraries like React. I know, I know, React isn't a framework but it serves a similar purpose by providing a foundation on which to build your application.


While I sympathize with people who are frustrated with the complex tooling that come with some libraries like React.


## Frameworks and libraries provide battle-tested solutions to common problems 

Server-side rendering, templating, routing — these aren’t easy problems to solve but they aren’t new either. Smart people have spent thousands of hours working on these problems. The frameworks and libraries they developed, as a result, have been tested for things like security, cross-browser support, and performance. And guess what? They’re free! So why not take advantage of that? By using a framework or library, your team can focus on delivering valuable features instead of wasting time reinventing the wheel.

## Frameworks guide best-practices for security 

Many frameworks provide a layer of protection against cross-site scripting (XSS). For instance, React uses automatic escaping to prevent developers from rendering untrusted HTML in the DOM. It's not bulletproof but it means that a developer with no knowledge of XSS is less likely to shoot themselves in the foot. 

With Vanilla JavaScript, this layer of protection doesn't exist. All it takes is an inexperienced developer to set `innerHTML` with some contaminated markup and 💥 you just got pwned.

## Frameworks guide best-practices for accessibility 

In addition to written guidelines, most frameworks provide plugins or tooling to improve accessibility. For example, Create React App comes with an eslint config that checks your code for common accessibility issues. While this doesn't guarantee AAA accessibility conformance, it at least points developers in the right direction. 

Some argue that accessibility is an afterthought in most frameworks. Even if that’s true, an afterthought is still better than no thought at all. A project built with Vanilla JavaScript provides no guidelines or tooling for accessibility. That might be fine if you’re an a11y expert but what about the rest of your team?

[React a11y Guidelines](https://reactjs.org/docs/accessibility.html)
[Ember a11y Guidelines](https://guides.emberjs.com/release/reference/accessibility-guide/)

## Frameworks provide a common vocabulary

After using a framework for a while, you start to speak its language. Components, props, state — these mean the same thing in any React project (I hope). That means a new hire with React experience should be able to ramp-up quickly on any React project. 

An app built with plain old JavaScript will take longer to ramp up on because it doesn’t follow conventions.

## Frameworks have opinions

The are so many ways to develop a modern web application that it can be overwhelming. Should you use react-router or reach-router, webpack or parcel? Opinionated frameworks like Ember make these decisions for you. That way your team can focus on what’s most important: building a great product.
