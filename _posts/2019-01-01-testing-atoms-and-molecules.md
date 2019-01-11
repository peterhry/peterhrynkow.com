---
layout: post
title: "Testing Your React App with Confidence"
date: 2019-01-01 00:00:00
categories: [testing]
icon: 🧠
---

What’s the best way to write tests for your React application so that you can feel confident deploying it to production? Where should you focus your testing effort and what tools should you use? These are questions I’ve been pondering for a while now so I thought I’d share my thoughts on the subject.

### Testing Atoms
I see a lot of React-Redux apps where components, action creators, and reducers are tested separately. It shouldn’t come as a surprise that a lot of teams follow this approach since it’s what’s described in the [Redux docs](https://redux.js.org/recipes/writing-tests). But is this the best method?

![Files]({{ site.baseurl }}/images/unit-tests.jpg)

Firstly, testing these elements in isolation doesn’t guarantee that they will work together. For example, a unit test for an [async action creator](https://redux.js.org/recipes/writing-tests#async-action-creators) asserts that a set of actions is dispatched but doesn’t ensure that a reducer is setup to handle it. Similarly, a unit test for a [reducer](https://redux.js.org/recipes/writing-tests#reducers) asserts that a new state is returned for a given action but doesn’t ensure that the component UI is updated to reflect the new state.

Secondly, most of these tests require you to mock some other part of the system. For example, the redux docs recommend using [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store) to test async action creators. As a result, you lose confidence in the integration between what you’re testing and the dependency being mocked.

This is an example of what I call testing the _atoms_ in your application. Knowing that these chunks of code work in isolation is great, but if you want to be confident that they work together, you should be testing the _molecules_.

### Testing Molecules

Components, action creators, and reducers are like atoms that when combined create a connected component _molecule_.

Here's an example:

```jsx
import React from 'react'
import {connect} from 'react-redux'

// Component (atom)
export const Counter = ({count, onClick}) => (
  <button title="Click Me" onClick={onClick}>Count: {count}</button>
)

// Action creator (atom)
export const incrementCounter = () => ({
  type: 'INCREMENT_COUNTER'
})

// Reducer (atom)
export const counterReducer = (state = {count: 0}, action) => {
  switch (action.type) {
    case 'INCREMENT_COUNTER':
      return {
        count: state.count + 1
      }
    default:
      return state
  }
}

// Connected component (molecule)
export default connect(
  (state) => state,
  {
    onClick: incrementCounter
  }
)(Counter)
```

For the sake of brevity, I put the action creator, reducer, and component in the same file, but in a real application, these elements would be stored separately.

The component in this example renders a button element that when clicked, increments a counter. Notice that the default export is a connected component which is what you want to test.

Here’s what the test looks like:

```jsx
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import {render, getByTitle, fireEvent} from 'react-testing-library'
import 'react-testing-library/cleanup-after-each'
import 'jest-dom/extend-expect'

import Counter, {incrementCounter, counterReducer} from '../'

// Create a real redux store
const store = createStore(counterReducer, {
  count: 0
})

it('increments the counter', () => {
  const {container} = render(
    <Provider store={store}>
      <Counter />
    </Provider>
  )

  const button = getByTitle(container, 'Click Me')

  fireEvent.click(button)
  expect(button).toHaveTextContent('Count: 1')

  fireEvent.click(button)
  expect(button).toHaveTextContent('Count: 2')
})
```

Notice that this test uses a real Redux store instead of [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store). This is so you can test the component from UI event through to UI update. 

It might surprise you that this test covers every line of code in the action creator, reducer, and component. By testing a molecule, you are indirectly testing its atoms.

More importantly, this test verifies the relationship between atoms. For example, the test breaks if the component's `onClick` prop is not mapped to the `incrementCounter` action creator or if the reducer fails to handle the `INCREMENT_COUNTER` action.

Integration tests like this one give you more confidence (beyond unit tests alone) that your app will work as expected. So instead of writing a unit test for every atom in your application, zoom out a bit, and write some integration tests for the molecules.

Don't get me wrong, there are still scenarios where it makes sense to write unit tests (shared libraries, modules
published to npm, etc.), but for testing the behaviour of your application, integration tests are more likely to catch problems.

### Tools
For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourage a black box testing approach. By following this method, you can easily refactor your code without breaking your tests. In contrast, [Enzyme](https://airbnb.io/enzyme/) has methods like [`setState`](https://airbnb.io/enzyme/docs/api/ReactWrapper/setState.html) and [`state`](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) that might tempt you to test a component’s implementation details. Doing so makes refactoring more difficult.

### In Summary

Integration tests give you more confidence in the reliability of your application because they verify the relationships between units of code. 

Unit tests are better for testing shared libraries or packages that you intend to distribute.