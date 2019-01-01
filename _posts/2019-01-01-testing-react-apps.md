---
layout: post
title:  "My approach to testing React apps"
date:   2019-01-01 00:00:00
categories: [testing]
icon: 🧠
---

What is the best way to write tests for your React application so that you can be confident deploying it to production? This is something I’ve been pondering for a while now so I thought I’d share my thoughts on the subject.

### Tools

There are so many different tools available for testing React apps that it can be overwhelming when you start writing tests.

For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourages a black-box testing approach. By writing tests this way, you can easily refactor your code without breaking your tests. I’ve also used enzyme in the past but found its API confusing.

For E2E test Cypress.io is pretty good.

### Redux

I see a lot of Redux apps where components, actions, reducers, and selectors are tested as separate units. This is the approach described in the [Redux docs](https://redux.js.org/recipes/writing-tests) so it shouldn’t come as a surprise that a lot of teams follow this direction. I recommend a different approach, however.

Testing these elements separately doesn’t ensure that your app will behave as expected when you put them together. Moreover, what I often see is that testing the integration between these elements is neglected. For this reason. I think unit tests make sense if you intend to package your code and publish it to npm, but for testing application behavior, integration tests are more likely to catch problems.

### Snapshots

Coming soon.


