---
layout: post
title: "The Perils of Jest Snapshot Testing"
date: 2019-01-07 00:00:00
categories: [testing]
icon: 🧠
---

According to the [Jest docs](https://jestjs.io/docs/en/snapshot-testing), snapshot tests help ensure your UI doesn’t change unexpectedly. That sounds great, but in practice snapshot tests can create more noise than signal. Here’s why.

Say you have a simple button component:

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

Now any change to the component’s rendered output will cause the test to fail. Sounds great, right?

Consider what happens when you add a new attribute `target` but mistype it as `traget`:

```jsx
const Button = ({href, target, children}) => (
  <a href={href} traget={target}>{children}</a>
)
```

You expect the test to fail, since the rendered output changed. In haste you skim the diff, miss the typo, and update the snapshot anyway. Now you have a passing test that asserts the wrong output. WTF just happened?

### Sorting the Catch

When a snapshot test fails, you must review each change and decide whether it’s a bug or a valid update. That review is tedious and error-prone, especially when lots of changes pile up.

![Files]({{ site.baseurl }}/images/fish.jpg)

Snapshot testing is like fishing with a giant net. You want bugs, but you also catch plenty of valid changes. Sorting the catch—deciding what to keep and what to throw back—is the hard part. The risk is updating a snapshot because you assume the change is correct, when in fact it’s a bug.

Things get worse with [jest-styled-components](https://github.com/styled-components/jest-styled-components), which stores style rules with the snapshot. Now you’re reviewing every line of changing CSS, too.

### Snapshot Fatigue

Snapshot tests are easy to create because they push all the cognitive load to the reviewer. After a while engineers experience _snapshot fatigue_ and start blindly updating failed snapshots without reviewing them. At that point the tests are useless.

### Do You Even Need a Snapshot?

A good test prevents you from accidentally breaking a component’s API. By creating a snapshot test, you’re essentially declaring that the component’s _entire_ rendered output is part of its API and should never change. Sometimes that’s true, but usually a component has specific behaviors you care about. Freezing its entire output makes refactoring painful.

Instead, identify the UI elements that are critical to behavior and test those explicitly.

```jsx
import React from 'react'
import Button from '../'
import {render, getByText} from 'react-testing-library'
import 'react-testing-library/cleanup-after-each'
import 'jest-dom/extend-expect'

it('renders correctly', () => {
  const {container} = render(<Button href="https://myurl.com" target="_blank">My Label</Button>)
  const button = getByText(container, 'My Label')

  expect(button).toHaveAttribute('target', '_blank')
  expect(button).toHaveAttribute('href', 'https://myurl.com')
})
```

This test is explicit. It verifies that the link contains the correct text and that its `href` and `target` values are correct. As long as those values don’t change, the component should function as expected.

The method requires more thought upfront but eliminates the burden and risk of manually reviewing snapshot diffs over time. It also lets you refactor freely as long as those specific values stay intact.
