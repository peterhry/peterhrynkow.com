---
layout: post
title: "The Perils of Snapshot Testing"
date: 2019-01-07 00:00:00
categories: [testing]
icon: 🧠
---

According to the Jest docs, snapshot tests are useful whenever you want to make sure your UI does not change unexpectedly. That sounds great in theory, but in this post, I will demonstrate some of the pain points and risks associated with snapshot testing.

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

Well, let me give you a scenario where things go wrong. Say you decide to add a new attribute `target` but accidentally name it `traget`.

```jsx
const Button = ({href, target, children}) => (
 <a href={href} traget={target}>{children}</a>
)
```

In your mind, you already expect the test to fail, since by adding a new attribute, you've changed the component's rendered output. 

In haste, you review the diff but fail to notice the typo. After updating the snapshot you commit your changes and push them to GitHub.

Now the snapshot test passes but makes the wrong assertion about your component's rendered output. Yikes.

WTF just happened?

### Sorting the Catch

When a snapshot test fails, you have to manually review each change and decide whether it's a bug or valid change. This process is tedious and prone to human error, especially when there are lots of changes to review.

Snapshot testing is sort of like fishing with a giant net. There's a certain kind of fish you want to catch (bugs), but you end up catching a lot of other stuff, too (valid changes). The hard part is sifting through your catch and deciding what to keep and what to throw back.

The risk is that you update a snapshot thinking a change was valid when it was in fact a bug.

Things get worse if you use [jest-styled-components](https://github.com/styled-components/jest-styled-components) because it stores your component's style rules with the snapshot. That means having to review every line of CSS that changes, too.

### Snapshot Fatigue

After a while, engineers begin to experience something I call _snapshot fatigue_ and start blindly updating failed snapshots. Once that happens, your snapshot tests are pretty much useless.

### Do You Need a Snapshot?

A good test should prevent you from accidentally breaking your component's API. By creating a snapshot test, you're essentially declaring that your component's _entire_ rendered output is part of its API.

In some cases that might be what you want, but often, you want some flexibility to add new features and refactor without breaking your tests. 




