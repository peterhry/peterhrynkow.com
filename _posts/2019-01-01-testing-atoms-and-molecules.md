---
layout: post
title: "‪A Better Way to Test Your React-Redux App‬"
date: 2019-01-01 00:00:00
categories: [testing]
icon: 🔬

---

I come across a lot of React-Redux apps where components, action creators, and reducers are tested as separate units. Does this sound familiar? It shouldn’t come as a surprise that a lot of teams follow this approach since it’s what’s described in the [Redux docs](https://redux.js.org/recipes/writing-tests).

But in this post I'll demonstrate why this method is insufficient when it comes to ensuring the stability of your application. I'll also show you a better — and frankly easier — way to test your React-Redux app that will give you more confidence deploying it to production.

### Testing Atoms

So what's wrong with testing components, action creators, and reducers separately?

Firstly, testing these elements in isolation doesn’t guarantee that they work together. The connections between these elements are not covered by unit tests. For example, a unit test for an [action creator](https://redux.js.org/recipes/writing-tests#action-creators) asserts that an action is created but doesn't verify that the action is ever dispatched. Similarly, a unit test for a [reducer](https://redux.js.org/recipes/writing-tests#reducers) asserts that a new state is returned for a given action but doesn’t ensure that the component UI is updated to reflect the new state. 

Secondly, because these tests require you to mock other parts of the system, you lose confidence in the integration between what you’re testing and the dependency being mocked. For instance, the Redux docs recommend using [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store) to test async action creators. A mock store _looks_ like a real Redux store, but unlike a real store, its state is completely static. You can verify that certain actions are dispatched but there's no telling how those actions will transform the state of your application when a real store is used.

Finally, these tests are so narrow in scope that it makes refactoring nearly impossible. Any small change to the code in these elements requires the tests to be updated.

This is an example of what I call testing the _atoms_ in your application. Knowing that these tiny chunks of code work in isolation is great, but if you want to be confident that they work together, you should be testing the _molecules_.

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

For the sake of brevity, I put the action creator, reducer, component, and connected component in the same file, but in a real application, these would be separate modules.

The component in this example renders a button element that when clicked, increments a counter. Notice that the default export is a connected component which is what you should test.

Here’s what the test looks like:

```jsx
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import {render, getByTitle, fireEvent} from 'react-testing-library'
import 'react-testing-library/cleanup-after-each'
import 'jest-dom/extend-expect'

import Counter, {counterReducer} from '../'

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

Notice that this test uses a real Redux store instead of [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store). **Using a real Redux store in your tests effectively closes the loop between UI event and UI update.** In most cases, your tests and production code can use the same store configuration.

It might surprise you that this test covers every line of code in the action creator, reducer, and component. **By testing a molecule, you are indirectly testing its atoms.** Just be sure to use a code coverage tool to ensure all your branches are covered.

More importantly, this test verifies the relationship between atoms. For example, the test breaks if the component's `onClick` prop is not mapped to the `incrementCounter` action creator or if the reducer fails to handle the `INCREMENT_COUNTER` action.

This is an example of a narrow integration test. Your component API (its props and UI) serve as the testing surface. Everything inside your component — including its reducers and action creators — is an implementation detail. Now you are free to refactor the guts of your component without having to update your tests.

> Many people assume integration tests are necessarily broad in scope, while they can be more effectively done with a narrower scope.<br>— Martin Fowler

In some cases it is still necessary to mock dependencies that live outside the molecule. For instance, you might need to mock the `fetch` API if your action creator makes an HTTP request. This is unavoidable unless you use a framework like Cypress or Selenium to test your app from end to end — more on that later.

Integration tests like this one give you more confidence (beyond unit tests alone) that your app will work as expected. So instead of writing a unit test for every atom in your application, zoom out a bit, and write some integration tests for the molecules.

There are scenarios where unit tests are still appropriate (shared libraries, modules
published to npm, etc.) but for testing the behaviour of your application, integration tests are more likely to catch problems.

### Tools

For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourage a black box testing approach. By following this method, you can easily refactor your code without breaking your tests. In contrast, [Enzyme](https://airbnb.io/enzyme/) has methods like [`setState`](https://airbnb.io/enzyme/docs/api/ReactWrapper/setState.html) and [`state`](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) that might tempt you to test a component’s implementation details. Doing so makes refactoring more difficult.

### In Summary

- Unit tests don't cover the connections between components, action creators, and reducers. 
- Integration tests give you more confidence in the reliability of your application because they verify the relationships between units of code.
- Integration tests don't have to be wide in scope. You can use them to test the connection between just a handful of modules. 
- Unit tests are better for testing shared libraries or packages that you intend to distribute.
