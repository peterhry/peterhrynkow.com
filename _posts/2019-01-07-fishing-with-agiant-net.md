---
layout: post
title: "Snapshot Testing is Trawling for Bugs"
date: 2019-01-07 00:00:00
categories: [testing]
icon: 🧠
---

According to the Jest docs, snapshot tests are useful whenever you want to make sure your UI does not change unexpectedly. Let's see just how useful they really are.

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

Well, let's say you add a new attribute `target` but accidentally name it `traget`.

```jsx
const Button = ({href, target, children}) => (
 <a href={href} traget={target}>{children}</a>
)
```

You already expect the test to fail, since by adding a new attribute, you've changed the rendered output. You review the diff but fail to notice the typo. In haste, you update the snapshot.

What just happened?

When a snapshot test fails, you have to review each change and decide if it was intentional (requiring the snapshot to be updated) or accidental (requiring a bug to be fixed). This process is tedious and prone to human error, especially when there are lots of changes to review. In this case, an intentional change (adding an attribute) introduced a new bug.

Generally speaking, there are three types of changes to review:

1. An intentional change like adding a new feature. For these you want to update the snapshot.
1. An accidental change that requires a bug fix.
1. An intentional change that introduces a new bug. These ones can be hard to spot.

It can be easy to confuse these types when you are reviewing a big diff.

Snapshot testing is sort of like fishing with a giant net. There's a certain type of fish you want to catch (bugs), but you end up catching a lot of other stuff, too (intentional changes). The hard part is sifting through your catch and deciding what to keep and what to throw back.

The risk is that you update a snapshot thinking a change was intentional when it was in fact accidental.

<!--
[jest-styled-components]() includes a component’s style rules in the snapshot. So using `toMatchSnapshot(tree)` will cause the test to fail if _any_ CSS rule has changed.
-->
