---
layout: post
title:  "My approach to testing React apps"
date:   2019-01-01 00:00:00
categories: [testing]
icon: 🧠
---

What is the best way to write tests for your React application so that you can feel confident deploying it to production? This is something I’ve been pondering for a while now so I thought I’d share my thoughts on the subject.

### Tools
For unit and integration tests, I like [react-testing-library](https://github.com/kentcdodds/react-testing-library) because its guiding principles encourage a black-box testing approach. By writing tests this way, you can easily refactor your code without breaking your tests. In contrast, Enzyme has methods like [`setState`](https://airbnb.io/enzyme/docs/api/ReactWrapper/setState.html) and [`state`](https://airbnb.io/enzyme/docs/api/ReactWrapper/state.html)that might tempt you to test a component’s internals.

For E2E test Cypress.io is pretty good but more on that later.

### What to test
I see a lot of Redux apps where components, action creators, reducers, and selectors are tested as separate units. This is the procedure described in the [Redux docs](https://redux.js.org/recipes/writing-tests) so it shouldn’t come as a surprise that a lot of teams follow it. There is a problem with this method, however.

Testing these elements separately, doesn’t ensure that your app will behave as expected when you bring them all together. Moreover, what I often find is that the code connecting these elements (`mapStateToProps`, `mapDispatchToProps`, etc.) is left untested. <!-- Take this component test for example:

```jsx
import {connect, Provider} from 'react-redux'
import {render, fireEvent} from 'react-testing-library'
import createStore from './createStore'

const GET_DATA = 'GET_DATA'

const getData = () => ({
  type: GET_DATA
})

const MyComponent = ({onClick}) => <button onClick={onClick}>Get data</button>

const mapDispatchToProps = {
  onClick: getData
}

const MyComponentWithRedux = connect(
  null,
  mapDispatchToProps
)(MyComponent)

describe('MyComponent', () => {
  it('calls onClick when the button is clicked', () => {
    const mockOnClick = jest.fn()
    const {container, getByText, debug} = render(
      <MyComponent onClick={mockOnClick} />
    )

    fireEvent.click(getByText('Get data'))
    expect(mockOnClick).toHaveBeenCalled()
  })
})
```

The test asserts that the `onClick` prop is invoked when the button is clicked, but sadly, `mapDispatchToProps` is not covered since the test does not assert that `GET_DATA` was dispatched. -->

In my opinion, action creators, reducers, and selectors are component implementation details that shouldn’t be tested directly. Instead, I treat each component as a black box and test it from the outside. Doing so covers the code in each element as well as the code that connects them.

[code example]

I’m not the first person to consider this approach to testing a Redux app. See [this thread](https://github.com/reduxjs/redux/issues/2179) in the Reux repo.

Unit tests make sense if you intend to package your code and publish it to npm, but for testing application behavior, integration tests are more likely to catch problems.



