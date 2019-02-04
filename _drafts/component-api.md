---
layout: post
title: "Component APIs"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use React or Polymer, CSS modules or styled components?  Engineers spend a lot of time thinking about the _how_ of building components. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to optimize.

Just as there are many ways to _implement_ a component, there are many ways to _design its API_. Having worked on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful API design.

The design of your API can have a lasting impact on the success and adoption of your component library. A well designed API is simple yet powerful, well-documented yet intuitive, and empowers the user to build interfaces quickly and with ease. In contrast, a poorly designed API is complicated, unintuitive, and frustrating.

> API design is hard because you can make it seem simple but it's actually deceptively complex, or make it actually simple but seem complex.
[sebmarkbage](https://twitter.com/sebmarkbage/status/728433349337841665)

As [@sebmarkbage](https://twitter.com/sebmarkbage) points out, API design is not without its challenges. In this post, I will share my thoughts on some common component API design problems.

But first, let’s go over the various parts of a component API.

### What is a Component API?

A component API is a contract between the component and its consumer. It tells the consumer — the user of the component — how to control the component and what to expect from it. In React, a component API includes:

#### The name and type of each prop

For example, this `Button` component expects a `boolean` value for its `disabled` prop and a `string` value for its `type` prop.

```jsx
import {render} from 'react-dom'
import Button from './components/button'

render(
  <Button disabled type="submit">
    Submit
  </Button>,
  document.getElementById('root')
)
```

#### The shape of object and array props

This `List` component expects its `items` prop to be an `array` where each element is an object with an `id: number` and `label: string` property.

```jsx
import {render} from 'react-dom'
import List from './components/list'

render(
  <List items={[{id: 1, label: 'One'}, {id: 2, label: 'Two'}]} />,
  document.getElementById('root')
)
```

#### The signature of function props

The consumer expects this component to call `onProgress` with `loaded: number` and `total: number`.

```jsx
import {render} from 'react-dom'
import Loader from './components/loader'

const onProgress = (loaded, total) => {
  const percentage = loaded / total
}

render(<Loader onProgress={onProgress} />, document.getElementById('root'))
```

#### The expected type of children

This `Tabs` component expects to receive `Tab` components as children.

```jsx
import {render} from 'react-dom'
import Tabs from './components/tabs'
import Tab from './components/tabs/tab'

render(
  <Tabs>
    <Tab id="1">One</Tab>
    <Tab id="2">Two</Tab>
    <Tab id="3">Three</Tab>
  </Tabs>,
  document.getElementById('root')
)
```

#### Rendered output

Anything rendered by the component should be considered part of its API.


