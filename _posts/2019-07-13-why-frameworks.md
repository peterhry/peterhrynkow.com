---
layout: post
title: "Why Use a JavaScript Framework"
date: 2019-07-13 00:00:00
categories: [javascript]
---

![Sketch of framework building blocks]({{ site.baseurl }}/images/illustration-why-frameworks.svg)

Recently, I was asked why using a framework or library like React, Angular, or Vue.js is better for building web apps than vanilla JavaScript. Here are the reasons I keep coming back to.

### Frameworks and libraries provide battle-tested solutions to common problems

Server-side rendering, templating, and routing are just a few challenges you’ll face when building an application. These problems aren’t new. Framework developers have spent thousands of hours solving them, so you don’t have to. Their solutions are battle-tested for performance, reliability, and browser compatibility. Why reinvent the wheel when you can stand on their shoulders and spend your time on features?

### Frameworks guide best practices for security

Many frameworks offer a layer of protection against cross-site scripting (XSS). For instance, React uses automatic escaping to [prevent developers from rendering untrusted HTML in the DOM](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml). It’s not bulletproof, but it means a developer with no security background is less likely to shoot themselves in the foot.

With vanilla JavaScript, that layer doesn’t exist. One careless `innerHTML` and 💥 you’ve got a problem.

### Frameworks guide best practices for accessibility

In addition to written guidelines, most frameworks provide tooling to improve accessibility. Create React App, for example, ships with an [eslint config](https://github.com/facebook/create-react-app/blob/master/packages/eslint-config-react-app/README.md#accessibility-checks) that checks your code for common accessibility issues. It doesn’t guarantee AAA accessibility, but it nudges developers in the right direction.

[React a11y Guidelines](https://reactjs.org/docs/accessibility.html)<br>
[Ember a11y Guidelines](https://guides.emberjs.com/release/reference/accessibility-guide/)

### Frameworks provide a common lexicon

After using a framework for a while, you start to speak its language. Components, props, state—these mean the same thing in any React project. Having a common lexicon improves communication and helps new team members ramp up quickly.

### Frameworks are inevitable

There’s a tipping point in most software projects where, if you didn’t start with a framework, you begin building one by accident. As you add features, complexity creeps in and you start generalizing pieces of code. Keep doing that and you end up with something that looks like a framework but with more bugs and less documentation than an off-the-shelf option.

Think about the features you could have been building while you were busy debugging that DOM manipulation code. Using a framework lets you focus on delivering value instead of reinventing the wheel.

### Frameworks have opinions

There are so many ways to build a modern web application that starting with vanilla JavaScript can lead to decision paralysis. How should your files be organized? How should your build scripts be configured? Opinionated frameworks like Ember make these decisions for you. That way your team can focus on what’s most important: building a great product.
