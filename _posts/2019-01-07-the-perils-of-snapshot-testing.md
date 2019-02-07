---
layout: post
title: "The Perils of Snapshot Testing"
date: 2019-01-07 00:00:00
categories: [testing]
icon: 🧠
---

According to the [Jest docs](https://jestjs.io/docs/en/snapshot-testing), snapshot tests are useful whenever you want to make sure your UI does not change unexpectedly. That sounds great in theory, but in this post, I will demonstrate some of the pain points and risks associated with snapshot testing.

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

Wow, that was easy. Now any change to the component’s rendered output will cause the test to fail. Sounds great, right?

Well, let me give you a scenario where things go off the rails. Say you decide to add a new attribute `target` but accidentally name it `traget`.

```jsx
const Button = ({href, target, children}) => (
  <a href={href} traget={target}>{children}</a>
)
```

In your mind, you already expect the test to fail, since by adding a new attribute, you've changed the component's rendered output. In haste, you review the diff but fail to notice the typo. After updating the snapshot you commit your changes and push them to GitHub.

Now you have a snapshot test that passes but makes the wrong assertion about your component's rendered output. WTF just happened?

### Sorting the Catch

When a snapshot test fails, you have to manually review each change and decide whether it's a bug or a valid change. This process is tedious and prone to human error, especially when there are lots of changes to review.

![Files]({{ site.baseurl }}/images/fish.jpg)

Snapshot testing is sort of like fishing with a giant net. There's a certain kind of fish you want to catch (bugs), but you end up catching a lot of other stuff, too (valid changes). The hard part is sorting the catch and deciding what to keep and what to throw back.

The risk is that you update a snapshot thinking a change was valid when it was in fact a bug.

Things get worse if you use [jest-styled-components](https://github.com/styled-components/jest-styled-components) because it stores your component's style rules with the snapshot. That means having to review every line of CSS that changes, too.

### Snapshot Fatigue

Snapshot tests are easy to create, requiring almost no forethought. That's because they place all of the cognitive burden on the reviewer.

After a while, engineers begin to experience something I call _snapshot fatigue_ and start blindly updating failed snapshots without reviewing them. Once that happens, your snapshot tests are pretty much useless.

### Do You Even Need a Snapshot?

A good test should prevent you from accidentally breaking your component's API. By creating a snapshot test, you're essentially declaring that your component's _entire_ rendered output is part of its API. In some cases that might be what you want but I'd argue that it makes adding new features and refactoring painful.

As an alternative you can identify the elements of your component's UI that are critical to its function and test those specifically.

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

The test above is explicit. It verifies that the link contains the correct text and that its `href` and `target` values are correct. This method requires a little more forethought but eliminates the burden and risk of manually reviewing snapshots down the road.

