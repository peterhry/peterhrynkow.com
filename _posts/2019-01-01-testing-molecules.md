---
layout: post
title: "Testing React Apps"
date: 2019-01-01 00:00:00
categories: [testing]
icon: 🧠
---

What’s the best way to write tests for your React application so that you can feel confident deploying it to production? Where should you focus your testing effort and what tools should you use? These are questions I’ve been pondering for a while now so I thought I’d share my thoughts on the subject.

### Testing Atoms
I see a lot of Redux apps where components, action creators, reducers, and selectors are tested separately. It shouldn’t come as a surprise that a lot of teams follow this approach since it’s what’s described in the [Redux docs](https://redux.js.org/recipes/writing-tests). But there are problems with this method.

![Files]({{ site.baseurl }}/images/unit-tests.jpg)

Firstly, testing these elements in isolation doesn’t guarantee that they will work together as a system. For example, a unit test for an [async action creator](https://redux.js.org/recipes/writing-tests#async-action-creators) asserts that a particular action is dispatched but doesn’t ensure that a reducer is configured to handle it. Similarily, a unit test for a [reducer](https://redux.js.org/recipes/writing-tests#reducers) asserts that a new state is returned for a given action but doesn’t ensure that the component UI is updated to reflect the new state.

Secondly, most of these tests require you to mock some other part of the system. For example, the redux docs recommend using [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store) to test async action creators. As a result, you lose confidence in the integration between what you’re testing and the dependency being mocked.

This is an example of what I call testing the _atoms_ in your application. Knowing that these chunks of code work in isolation is great, but if you want to be confident that they work together, you should be testing the _molecules_.

### Testing Molecules

Action creators, reducers, and selectors are like atoms that when combined with a component create a molecule. By testing a molecule you’re indirectly testing its atoms, but more importantly, you’re ensuring that the atoms work together. So instead of writing a unit test for every atom in your application, zoom out a bit and write some integration tests for the _molecules_.

This means testing your connected components from UI event to UI update. Here is an example:

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

// Connected component
export default connect(
  (state) => state,
  {
    onClick: incrementCounter
  }
)(Counter)
```

Here’s how you test the connected component from UI event to UI update:

```jsx
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import {render, getByTitle, fireEvent} from 'react-testing-library'
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

It’s best to think of a component as a black box and test its behavior from the outside using the UI. The component under test might be made up of many smaller components as well as dependencies (reducers, action creators, etc.) but with the help of a code coverage tool, you can ensure that the code in these elements is covered by your tests.

Unit tests still make sense if you intend to package your code and publish it to npm, but for testing application behavior, integration tests are more likely to catch problems.

### Fishing with a Giant Net

According to the Jest documentation, snapshot tests are useful whenever you want to make sure your UI does not change unexpectedly.


Let’s say you have a simple button component:

```jsx
const Button = ({href, children}) => (
 <a href={href}>{children}</a>
)
```

You can create a snapshot test for it like so:

```jsx
import React from 'react'
import Button from '../'
import renderer from 'react-test-renderer'

it('renders correctly', () => {
  const tree = renderer
    .create(<Button href="https://myurl.com">My Label</Button>)
    .toJSON()
  expect(tree).toMatchSnapshot()
})
```

Now any change to the component’s rendered output will cause the test to fail. Sounds great, right? But what if you change something innocuous, like this?

```jsx
const Button = ({url, children}) => (
 <a href={url}><span>{children}</span></a>
)
```
Now you have to review the snapshot diff to determine if the change was intentional (requiring the snapshot to be updated) or accidental (requiring a bug to be fixed). The more changes to review,
the more tedious and error-prone this process becomes.

Snapshot testing is sort of like fishing with a giant net. Sure, you catch a lot of fish (bugs), but you catch a lot of other stuff, too. Then you have to sift through it all and decide what to keep and what to throw back.

The risk is that you update a snapshot thinking a change was intentional when it was in fact a bug that caused the test to fail. For this reason, I avoid creating snapshot tests for large branches of components.

<!--

[jest-styled-components]() includes a component’s style rules in the snapshot. So using `toMatchSnapshot(tree)` will cause the test to fail if _any_ CSS rule has changed.

A component’s styles generally fall into two categories:

1. styles that derive their value from state or props
2. styles with values that never change throughout the component lifecycle


Say you have a button component and you change the font size of its label. A snapshot test for this component would break but is this what you want?

There are likely some CSS properties that you care about more than others. For example, you might want to test that the button `background-color` is grey when the `disabled` prop is true. The colour of the button is coupled to its business logic. These properties are typically derived from the component’s props or state.

But the since the font size has no connection to the component’s business logic, why test it at all?

A good test should allow you to refactor a component’s implementation so long as you don’t break its public API. So should styling be considered part of a component’s public API?

It depends.

In this case, I’d say it’s more important that the button label exists and that its content is correct.

If there are critical styles that you want to check it might be better to target these specifically.

In most cases, you should be free to change a component’s styling without having to update your tests.

Link to CircleType.

Snapshot tests make sense when styling is considered a part of your component’s public API. -->

### Tools
For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourage a black box testing approach. By following this method, you can easily refactor your code without breaking your tests. In contrast, [Enzyme](https://airbnb.io/enzyme/) has methods like [`setState`](https://airbnb.io/enzyme/docs/api/ReactWrapper/setState.html) and [`state`](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html) that might tempt you to test a component’s implementation details. Doing so makes refactoring more difficult.

<!-- I like to use [Jest DOM](https://www.npmjs.com/package/jest-dom) for making assertions about the state of a DOM element.

Tools I avoid:

- [jest-styled-components]()
- [redux-mock-store]()
- [Enzyme](https://airbnb.io/enzyme/) -->

### Conclusion
TBC
