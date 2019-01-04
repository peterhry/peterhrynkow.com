---
layout: post
title:  "My approach to testing React apps"
date:   2019-01-01 00:00:00
categories: [testing]
icon: 🧠
---

What’s the best way to write tests for your React application so that you can feel confident deploying it to production? This is something I’ve been pondering for a while now so I thought I’d share my thoughts on the subject.

### Tools
For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourage a black-box testing approach. By writing tests this way, you can easily refactor your code without breaking your tests. In contrast, [Enzyme](https://airbnb.io/enzyme/) has methods like [`setState`](https://airbnb.io/enzyme/docs/api/ReactWrapper/setState.html) and [`state`](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) that might tempt you to test a component’s internal implementation.

For E2E tests, Cypress.io is pretty good but more on that later.

### What not to test
I see a lot of Redux apps where separate unit tests are created for components, action creators, reducers, and selectors. It shouldn’t come as a surprise that a lot of teams follow this approach since it’s what’s described in the [Redux docs](https://redux.js.org/recipes/writing-tests). But there’s a problem with this method.

![Files]({{ site.baseurl }}/images/unit-tests.jpg)

Testing these elements separately, doesn’t gurantee that your app will behave as expected when you bring them together. For example, a unit test for an [async action creator](https://redux.js.org/recipes/writing-tests#async-action-creators) can assert that a particular action was dispatched, but it doesn’t gurantee that a reducer will handle said action. Moreover, what I often find is that the code connecting these elements (`mapStateToProps`, `mapDispatchToProps`, etc.) is left untested.

Action creators, reducers, and selectors are component implementation details that shouldn’t be tested directly.

### What to test

> Write tests. Not too many. Mostly integration.<br>
[@rauchg](https://twitter.com/@rauchg)

This is a great piece of wisdom from Guillermo Rauch.

Think of it like this: Instead of writing a unit test for every _atom_ in your application, zoom out a bit and write some integration tests for the _molecules_. Action creators, reducers, and selectors are atoms that when combined form a connected component molecule. By testing a molecule you are also testing its atoms. But more importantly you're ensuring that those atoms work together.

I like to think of a component as a black box and test it from the UI. The component under test might be made up of many smaller components as well as dependencies (reducers, action creators, etc.) but with the help of a code coverage tool, you can verify that these elements are covered by your tests.

Unit tests make sense if you intend to package your code and publish it to npm, but for testing application behavior, integration tests are more likely to catch problems.

More to come.

### Examples
Coming soon

### Snapshot testing
Coming soon
