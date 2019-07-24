---
layout: post
title: "Vanilla JavaScript Doesn’t Cut it"
date: 2019-07-13 00:00:00
categories: [javascript]
---

Recently, I was asked why using a framework or library like React, Angular, or Vue.js is better for building web apps than vanilla JavaScript. Some folks claim that [JavaScript frameworks are unnecessary](https://dev.to/gypsydave5/why-you-shouldnt-use-a-web-framework-3g24) and that [platform APIs are all you need](https://gomakethings.com/javascript-frameworks-are-better-for-accessibility-and-other-myths/) to build a web app these days. Here's why they're wrong:

### Frameworks and libraries provide battle-tested solutions to common problems 

Server-side rendering, templating, routing — these aren’t easy problems to solve but they aren’t new either. Smart people have spent thousands of hours working on these problems. The frameworks and libraries they've developed as a result have gone through rounds of bug fixes and testing for things like security, browser compatibility, and performance. So why not stand on the shoulders of giants? By using a framework or library, your team can focus on delivering valuable features instead of reinventing the wheel with vanilla JavaScript.

### Frameworks guide best practices for security 

Many frameworks provide a layer of protection against cross-site scripting (XSS). For instance, React uses automatic escaping to [prevent developers from rendering untrusted HTML in the DOM](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml). It's not bulletproof but it means that a developer with no knowledge of XSS is less likely to shoot themselves in the foot. 

With vanilla JavaScript, this layer of protection doesn't exist. All it takes is an inexperienced developer to set `innerHTML` with some contaminated markup and 💥 you just got pwned.

### Frameworks guide best practices for accessibility 

In addition to written guidelines, most frameworks provide plugins or tooling to improve accessibility. For example, Create React App comes with an [eslint config](https://github.com/facebook/create-react-app/blob/master/packages/eslint-config-react-app/README.md#accessibility-checks) that checks your code for common accessibility issues. While this doesn't guarantee AAA accessibility conformance, it at least points developers in the right direction. 

Some argue that accessibility is an afterthought in most frameworks. Even if that’s true, **an afterthought is still better than no thought at all.** 😎

[React a11y Guidelines](https://reactjs.org/docs/accessibility.html)<br>
[Ember a11y Guidelines](https://guides.emberjs.com/release/reference/accessibility-guide/)

### Frameworks provide a common lexicon 

After using a framework for a while, you start to speak its language. Components, props, state — these mean the same thing in any React project. Having a common lexicon improves communication and helps new team members ramp-up quickly.

### Frameworks are inevitable

There is a tipping point in most software projects where if you didn't start with a framework, you inevitably start building one by accident. As you add more features, complexity creeps in and you start generalizing areas of your code. Keep doing that for a while and what you end up with is something that looks like a framework but with more bugs and less documentation than something off the shelf.

Think about the features you could have been building while you were busy debugging that DOM manipulation code. Again, using a framework allows you to focus on valuable features instead of reinventing the wheel.

### Frameworks have opinions

The are so many ways to develop a modern web application that starting a project with vanilla JavaScript can lead to decision paralysis. How should your files be organized? How should your build scripts be configured? Opinionated frameworks like Ember make these decisions for you. That way your team can focus on what’s most important: building a great product.


