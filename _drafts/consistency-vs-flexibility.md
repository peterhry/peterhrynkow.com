---
layout: post
title: "Consistency vs Flexibility in Design Systems"
date: 2019-01-26 00:00:00
categories: [api]
---

Pattern libraries, component-based design systems — whatever you want to call them — it’s no secret that they improve user experience by increasing visual and functional consistency. Most designers will tell you that consistency is a key design principle. But how far should you go to prevent users from changing the appearance and behavior of your components?

Should a component enforce visual consistency by restricting access to its template and styles, or give the user options for overriding its appearance? Is it the role of technology (your components) or people within your organization to enforce consistency?

If you’ve ever worked on a pattern library project, you might have found yourself asking some of these questions. And they're good questions to ask, because the decisions you make in this regard, will no doubt inform the design of your component APIs.

In this post I will discuss an area of component API design where consistency and flexibility almost always come into play. The approach you take will likely depend on the level of flexibility with which you intend to provide your component consumers.

### Child Composition

In React, there are several ways to compose child elements. Some solutions provide the consumer with more flexibility than others. Let's look at a few options.

#### Array or object prop

Using an `array` or `object` prop to compose child elements offers very little flexibility because the component controls how the children are rendered. This approach is suitable for component libraries that prioritize consistency over flexibility.

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

Pros:
- Ensures that the same type of child component is always rendered.
- The consumer doesn't need to know what type of child component to supply.

Cons:
- The consumer has no control over child component being rendered so there is no opportunity to specify its type and props.
- Documenting the required data structure is awkward.

#### Children Prop

Using the `children` prop to compose child elements is idiomatic in React. This approach is a bit more flexible because it lets the consumer to render the children. 

However, the results is a tight coupling between the component and its children. For the component to control its children, they must expose the correct props.

Here the component is saying “Give me the children and I'll slot them in somewhere. Make sure they implement these props so I can control them".

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

Pros:
- Allows the consumer to specify the type of each child component and its props.

Cons:
- Unless you define a custom `propType` function, the consumer could pass in the wrong type of child component.

#### Render Prop

Using a render prop to compose child elements is an advanced pattern that delegates all aspects of child rendering to the consumer. Furthermore, it provides the consumer with details about the component's state that can be mapped specific child props. This is an example of [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control#Examples).

Here the component is saying “Render the children when I tell you to, and by the way, here is some information about my state in case you need it."

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
        <CustomTab key={item.id} selected={item.id === selectedTabId}>
          {item.label}
        </CustomTab>
      ))
    }
  />,
  document.getElementById('root')
)
```

In this example, the `Tab` component delegates rendering of its tabs to the consumer. When the component is ready to render, it calls the `renderTabs` function and passes it `selectedTabId`. This allows the consumer to render `CustomTab` children and set the `selected` prop on the appropriate child.


#### Which approach is best?

It depends whether your component library prioritizes consistency or flexibility. If you want to lock-down the design and behaviour of your components then an `array` prop is best. If you want ultimate flexibility then go for a render prop.