---
layout: post
title:  "Testing React Apps"
date:   2019-01-01 00:00:00
categories: [testing]
icon: 🧠
---

What’s the best way to write tests for your React application so that you can feel confident deploying it to production? What tools should you use and where should you focus your testing effort? These are questions I’ve been pondering for a while now so I thought I’d share my thoughts on the subject.

### Tools
For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourage a black-box testing approach. By writing tests this way, you can easily refactor your code without breaking your tests. In contrast, [Enzyme](https://airbnb.io/enzyme/) has methods like [`setState`](https://airbnb.io/enzyme/docs/api/ReactWrapper/setState.html) and [`state`](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) that might tempt you to test a component’s implementation details. Doing so makes refactoring impossible.

### What not to test
I see a lot of Redux apps where separate unit tests are created for components, action creators, reducers, and selectors. It shouldn’t come as a surprise that a lot of teams follow this approach since it’s what’s described in the [Redux docs](https://redux.js.org/recipes/writing-tests). But there’s a problem with this method.

![Files]({{ site.baseurl }}/images/unit-tests.jpg)

Testing these elements separately doesn’t guarantee that your app will behave as expected once they are combined. For example, a unit test for an [async action creator](https://redux.js.org/recipes/writing-tests#async-action-creators) asserts that a particular action is dispatched but doesn’t ensure that a reducer is configured to handle it. Similarily, a unit test for a [reducer](https://redux.js.org/recipes/writing-tests#reducers) asserts that a new state is returned for a given action but doesn’t ensure that the UI is updated to reflect the new state.

Most of these tests require you to mock other parts of the system, meaning you lose some confidence in the integration between what you’re testing and what’s being mocked. To be confident that your components, action creators, reducers, and selectors will work together, you need to test them together.

Unit tests make sense if you intend to package your code and publish it to npm, but for testing application behavior, integration tests are more likely to catch problems.

### What to test

Action creators, reducers, and selectors are like _atoms_ that when combined form a connected component _molecule_. Instead of writing a unit test for every _atom_ in your application, zoom out a bit and write some integration tests for the _molecules_. By testing the molecule you’re also testing its atoms, but more importantly, you’re ensuring that those atoms work together.

It’s best to think of a component as a black box and test its behavior from the outside using the UI. The component under test might be made up of many smaller components as well as dependencies (reducers, action creators, etc.) but with the help of a code coverage tool, you can ensure that the code in these elements is covered by your tests.

### Snapshot testing
One thing I’ve observed is that it’s easy to create a snapshot test for a component, but hard to debug it when it fails. The diff produced by a failing snapshot test can be difficult to read, particularly for large components. For this reason, I will avoid creating a snapshot test for a large branch of components.

Perhaps a side effect of difficult debugging is that snapshots are often updated blindly during a refactor because the developer assumes the changes were intentional.
