---
layout: post
title: "Consistency vs Flexibility in Pattern Libraries"
date: 2019-01-26 00:00:00
categories: [api]
---

Pattern libraries, component-based design systems — whatever you want to call them — it’s no secret that they improve user experience by increasing visual and functional consistency. Most designers will tell you that consistency is a key design principle. But how far should you go to prevent users from changing the appearance and behavior of components in your design system?

Should a component enforce visual consistency by restricting access to its template and styles, or give the user options for overriding its appearance? Is it the role of technology (your components) or people within your organization to enforce consistency?

If you’ve ever worked on a pattern library project, you might have found yourself asking some of these questions. And they're good questions to ask, because the decisions you make in this regard, will no doubt inform the design of your component APIs.

In this post I will discuss an area of component API design where the struggle between consistency and flexibility come into play. The approach you take will likely depend on the level of flexibility you intend to provide.

### Child Composition

In React, there are several ways to compose child elements. Some solutions provide the consumer with more flexibility than others. Let's look at a few options.

#### Array or object prop

Using an `array` or `object` prop to compose child elements is the least flexible option because the component controls what type of child is rendered and its props. This approach is suitable for component libraries that prioritize consistency over flexibility because the component controls all aspects of rendering.

Here the component is saying “Just give me the data — I'll take care of rendering the children".

```jsx
import {render} from 'react-dom'
import Tabs from './components/tabs'

render(
  <Tabs
    items={[
      {id: 1, label: 'One'},
      {id: 2, label: 'Two'},
      {id: 3, label: 'Three'}
    ]}
  />,
  document.getElementById('root')
)
```

<!-- Pros:
- Ensures that the same type of child component is always rendered.
- The consumer doesn't need to know what type of child component to supply.

Cons:
- The consumer has no control over child component being rendered so there is no opportunity to specify its type and props.
- Documenting the required data structure is awkward.
 -->
#### Children Prop

Using the `children` prop to compose child elements is idiomatic in React. This approach is a bit more flexible because it lets the consumer render the children.

However, the result is a tight coupling between the component and its children. For the component to control its children, they must expose the necessary props.

Here the component is saying “Give me the children and I'll slot them in somewhere. But make sure they expose these props in case I need to set them".

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

```jsx
import {Component, cloneElement} from 'react'

class Tabs extends Component {
  constructor() {
    super()

    this.state = {
      selectedTabId: null
    }
  }
  onTabClick() {
  }
  render() {
    return (
      <ul>
        {this.props.children.map((tab) => {
          return cloneElement(tab, {
            onClick: this.onTabClick
          })
        })}
      </ul>
    )
  }
}
```

<!-- Note: It’s possible to define a custom `propType` function that verifies the children are of the correct type. This effectively dials back the amount of control offered to the consumer.
 -->

#### Render Prop

Using a render prop to compose child elements is an advanced pattern that delegates all aspects of rendering to the consumer. Furthermore, it provides the consumer with details about the component's state that can be mapped to the desired child props. The result is a loose coupling between the component and its children.

Here the component is saying “Render the children when I call this function. I'll pass you some information about my state in case you need it."

```jsx
import {render} from 'react-dom'
import Tabs from './components/tabs'
import CustomTab from './components/custom-tab'

const items = [
  {id: 1, label: 'One'},
  {id: 2, label: 'Two'},
  {id: 3, label: 'Three'}
]

render(
  <Tabs
    renderTabs={(selectedTabId) =>
      items.map((item) => (
        <CustomTab key={item.id} active={item.id === selectedTabId}>
          {item.label}
        </CustomTab>
      ))
    }
  />,
  document.getElementById('root')
)
```

In this example, the `Tab` component delegates rendering of its tabs to the consumer. When the component is ready to render, it calls the `renderTabs` function and passes it `selectedTabId`. This allows the consumer to render `CustomTab` children and set the `active` prop on the appropriate child.

See also: [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control#Examples)

#### Which approach is best?

It depends whether your component library prioritizes consistency or flexibility. If you want to lock-down the design and behaviour of your components then an `array` prop might be best. If you want to provide your users with ultimate flexibility, then go for a render prop.
