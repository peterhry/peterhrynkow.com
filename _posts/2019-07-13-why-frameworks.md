---
layout: post
title: "Why You Should Use a JavaScript Framework"
date: 2019-07-13 00:00:00
categories: [programming]
---

Lately I've noticed some folks on Twitter who insist that JavaScript frameworks are overly-complicated, bloated, and unnecessary. They claim that “Vanilla JS” is all you need to build a web application these days. Here are some examples:


The backlash against frameworks and libraries for web development is in some ways understandable. 

That may be true in some cases. Using React to build a personal blog might be overkill, then again, I've seen some amazing stuff built with Gatsby.



I get it, tooling required for transpiling and bundling, can make getting into web development today feel overwhelming. 

For some projects, a framework is overkill. I wouldn't recommend Angular for a blog.


> I don't bother with frameworks, I just use vanilla JavaScript.

> Roughly translated:

> I'm smarter than the thousands of people who tried to solve the problems I'm about to solve. I’m an expert on security, a11y, browser support, and performance. I don't care about ROI, I just want to code.

I wanted to follow this up with a blog post because it's hard to provide context in 240 characters.

First a bit of context, I'm using the term "framework" pretty loosely here. I'm referring to proper frameworks like Ember, Angular, and Vue.js, as well as libraries like React. I know, I know, React isn't a framework but it serves a similar purpose by providing a foundation on which to build your application.



While I sympathize with people who are frustrated with the complex tooling that come with some libraries like React.


## Frameworks provide battle-tested solutions to common problems 

By using a framework, a team can focus on delivering valuable features instead of wasting time reinventing the wheel. For example, Next.js comes out of the box with features like server-side rendering, code splitting, and client-side routing.

By building on top of these solutions, a team can focus on building that new analytics dashboard instead of wasting time debugging 


## Frameworks guide best-practices for security 

Many frameworks provide a layer of protection against cross-site scripting (XSS). For instance, React uses automatic escaping to prevent developers from rendering untrusted HTML in the DOM. It's not bulletproof but it means that someone with no knowledge of XSS is less likely to shoot themselves in the foot. 

With Vanilla JS, this layer of protection doesn't exist. All it takes is an inexperienced developer to set `innerHTML` with some contaminated markup and 💥 you just got pwned.

## Frameworks guide best-practices for accessibility 

create-react-app comes with an eslint config that checks your code for common accessibility issues. While this doesn't guarantee AAA accessibility conformance, it at least points developers in the right direction.

## Frameworks provide a common vocabulary

After using a framework for a while, you start to speak its language. Components, props, state — these mean the same thing in any React project (I hope). That means a new hire with React experience should be able to ramp-up quickly on any React project. 

An app built with plain old JavaScript might take longer to ramp up on because it doesn’t follow conventions.

## Frameworks have opinions

The are so many ways to develop a modern web application that it can be a bit overwhelming. Should you use react-router or reach-router, webpack or parcel? Opinionated frameworks like Ember make these decisions for you. That way your team can focus on what’s most important: building a great product.
