---
layout: post
title: "‪A Better Way to Test Your React-Redux App‬"
date: 2019-01-01 00:00:00
categories: [testing]
icon: 🔬

---

I come across a lot of React-Redux apps where components, action creators, selectors, and reducers are tested as separate units. It’s a common practice—it’s even described in the [Redux docs](https://redux.js.org/recipes/writing-tests)—but there’s a better way.

In this post, I'll show you why the standard approach for testing React-Redux apps is insufficient and makes refactoring harder. I’ll also show you an easier way to test your app that catches more bugs and keeps refactors safe.

### Testing Atoms

So what's the problem with testing components, action creators, selectors, and reducers separately?

First, testing these elements in isolation doesn’t guarantee that they work together. A unit test for an [action creator](https://redux.js.org/recipes/writing-tests#action-creators) asserts that an action is created but doesn't verify that the action is ever dispatched. A unit test for a [reducer](https://redux.js.org/recipes/writing-tests#reducers) asserts that a new state is returned but doesn’t verify that the UI updates to reflect it. There’s a disconnect.

![Passing unit tests, sinking ship]({{ site.baseurl }}/images/unit-tests.jpg)

Second, because these tests require you to mock other parts of the system, you lose confidence in the integration between what you’re testing and the dependency being mocked. For example, the Redux docs recommend [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store) for async action creators. A mock store _looks_ like a real Redux store, but its state is static. It lets you verify that certain actions are dispatched but tells you nothing about how those actions change real state.

Finally, these tests are so granular that refactoring becomes painful. A small change to one module often requires updates to several tests. This slows development and increases the chance of new bugs.

That’s what I call testing the _atoms_. Knowing that tiny chunks of code work in isolation is great, but to be confident they work together, test the _molecules_.

### Testing Molecules

Components, action creators, selectors, and reducers are like atoms that combine to create a connected component _molecule_. Testing the molecule verifies the connections between its atoms.

Here's an example:

```jsx
// Component (atom)
import React from 'react'

const Counter = ({count, onClick}) => (
  <button title="Click Me" onClick={onClick}>Count: {count}</button>
)

export default Counter
```

```jsx
// Action creator (atom)
export const incrementCounter = () => ({
  type: 'INCREMENT_COUNTER'
})
```

```jsx
// Reducer (atom)
const counterReducer = (state = {count: 0}, action) => {
  switch (action.type) {
    case 'INCREMENT_COUNTER':
      return {
        count: state.count + 1
      }
    default:
      return state
  }
}

export default counterReducer
```

```jsx
// Connected component (molecule)
import {connect} from 'react-redux'
import {incrementCounter} from '/actions'
import Counter from './counter'

export default connect(
  (state) => state,
  {
    onClick: incrementCounter
  }
)(Counter)
```

The component renders a button that increments a counter when clicked.

Here’s what the molecule test looks like:

```jsx
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import {render, getByTitle, fireEvent} from 'react-testing-library'
import 'react-testing-library/cleanup-after-each'
import 'jest-dom/extend-expect'

import Counter from '../'
import counterReducer from '/reducers/counter-reducer'

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

A few takeaways:

- The connected component is the only module under test, but the test covers every line inside the other modules. **By testing a molecule, you indirectly test its atoms.**
- The test verifies the connections between atoms. It fails if the component’s `onClick` prop isn’t wired to `incrementCounter` or if the reducer mishandles the `INCREMENT_COUNTER` action.
- The test uses a real Redux store instead of [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store). **Using a real store closes the loop between UI event (input) and UI update (output).** You fire an event and assert that the UI updates. With a mock store, you can only assert that actions were dispatched.

Because the test focuses on behavior rather than implementation details, you can refactor the atoms without breaking it.

> Many people assume integration tests are necessarily broad in scope, while they can be more effectively done with a narrower scope.

[@martinfowler](https://twitter.com/martinfowler)

Narrow integration tests give you more confidence in the stability of your application because they verify the connections between smaller units of code. Instead of writing a unit test for every atom in your app, zoom out and write integration tests for the molecules.

> Write tests. Not too many. Mostly integration.

[@rauchg](https://twitter.com/rauchg)

There are plenty of scenarios where unit tests make sense (shared libraries, TDD, etc.) but for testing the overall behaviour of your application, integration tests are more likely to catch problems.

### Summary

- Unit tests don't cover the connections between components, action creators, selectors, and reducers.
- Integration tests give you more confidence in the stability of your application because they verify the relationships between units of code.
- Testing a connected component from the UI allows you to refactor its implementation freely.
- Integration tests don't have to be wide in scope. You can use them to test the connection between just a handful of modules.
