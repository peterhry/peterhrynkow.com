---
layout: post
title: "Snapshot Testing is Trawling for Bugs"
date: 2019-01-07 00:00:00
categories: [testing]
icon: 🧠
---

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

Now any change to the component’s rendered output will cause the test to fail. Sounds great, right? 

Well, let's say you add a new attribute `target` but accidentally name it `traget`.

```jsx
const Button = ({href, target, children}) => (
 <a href={href} traget={target}>{children}</a>
)
```

You expect the test to fail since you changed the rendered output, but you don't notice the typo. In haste, you update the snapshot.

What just happened?

When a snapshot test fails, you have to review each change and decide if it was intentional (requiring the snapshot to be updated) or accidental (requiring a bug to be fixed). The more changes to review, the more tedious and error-prone this process becomes.

Snapshot testing is sort of like fishing with a giant net. Sure, you catch a lot of fish, but you catch a lot of other stuff, too. Then you have to sift through it all and decide what to keep and what to throw back.

The risk is that you update a snapshot thinking a change was intentional when it was in fact accidental. For this reason, I avoid creating snapshot tests for large branches of components.

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