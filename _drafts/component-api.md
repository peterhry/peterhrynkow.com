---
layout: post
title: "The Elements of Component API Design"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components, React or Polymer? The _how_ of building UI components is something engineers spend a lot of time thinking about. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to optimize.

Just as there are many ways to _implement_ a component, there are many ways to _design its API_, and yet, engineers seem to invest less time in this area. Having worked on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful API design.

Whether you're building an application or component-based design system, the decisions you make with regards to API design can have a lasting impact on the success of your project. A well designed API provides useful abstractions, is simple yet powerful, well-documented yet intuitive — and most importantly — helps the consumer achieve some goal.

Component API design is not without its challenges, though. In this post, I will share my experience with solving some common problems that engineering teams face when designing component APIs.

### What is a Component API?

For a component to be useful, it needs to expose an API. The API allows the consumer to control and interact with the component. In React, it includes the component's props, children, callbacks, and rendered output. In Polymer, it includes other things like instance methods and events but I won't cover those here.

#### Props

The name, type, and shape of each prop.

```html
<Button disabled type="submit">Submit</Button>
```

#### Children

The accepted type and order of children.

```html
<Tabs>
  <Tab id="1">One</Tab>
  <Tab id="2">Two</Tab>
  <Tab id="3">Three</Tab>
</Tabs>
```

#### Callbacks

Callbacks and their signature,  supplied as function props.

#### Rendered Output

Anything rendered by the component. In some cases this can include its styles.

### API Design Problems

#### Customization

It’s no secret that component-based design systems improve user experience by increasing visual consistency. That being said, consumers often want to customize the appearance of components and it can be difficult to reconcile these two opposing ideas.

Should a component enforce visual consistency by restricting access to its template and styles, or is it the role of humans within the organization to police visual consistency?

If customization is supported, to what degree should a component allow the consumer to override its appearance? Should a component allow its internal elements to be styled directly or limit the consumer to choosing a predefined theme (i.e. light, dark)? Should a component allow any part of its template to be customized?

How you answer these questions is likely to impact the design of your component APIs.

#### Abstractness

Is it better to have one component that does five things or five components that each do one thing?

For example, you could create one video component that supports multiple video providers (i.e. YouTube, Vimeo, Twitch) or multiple video components that each support one provider.

Engineers gravitate toward higher levels of abstractions because they reduce the amount of code repetition. But abstraction comes at a cost — especially if done incorrectly.

TBC

#### Composition

How should your component API support composition?

https://twitter.com/brad_frost/status/1090733766950223878?s=21

Composition via children is idiomatic in React and web components.

```jsx
<Tabs>
  <Tab>One</Tab>
  <Tab>Two</Tab>
  <Tab>Three</Tab>
</Tabs>
```

One thing to note about this pattern, is that it gives the impression that the component will slot the children into its template as is, but that's not always the case.

A component can iterate over its children and render different components in their place:

// add example

Composition via props is unorthodox:

```jsx
<Tabs items={[
  {label: 'One'},
  {label: 'Two'},
  {label: 'Three'}
]} />
```

Composition via render props
```jsx
const items = [
  {id: 1, label: 'One'},
  {id: 2, label: 'Two'},
  {id: 3, label: 'Three'}
]

<Tabs renderItems={(selected) =>
  items.map(item =>
    <CoolTab selected={item.id === selected}>{item.label}</CoolTab>
  )
} />
```

TBC

#### Static vs Dynamic

Do you even need a client-side library to render your component?

If your components are used to render static documents like blog posts or documentation pages, you might not need a client-side component library.


#### Versioning

When distributing your component library, it's easy to introduce a new feature but much harder to remove it once a consumer depends on it.



TBC


<!--
### Boolean vs Enum

TBC



The design of a component's API has huge implications for the consumer:

A well designed API is intuitive.

#### Encapsulation
A well designed API hides complexity without restricting the consumer from achieving their goal. Compare these two car interiors.

![BMW Interior]({{ site.baseurl }}/images/bmw-interior.jpg)

![Tesla Interior]({{ site.baseurl }}/images/tesla-interior.jpg)

The BMW exposes complexity which makes its interface feel overwhelming. In contrast, the Tesla — which has more capabilities than the BMW —  hides complexity behind its touch screen which makes its interface feel less intimidating.

A well designed component API is flexible in that it can be configured and used in different places without the exposing too many options as to overwhelm the consumer.





### Component API Characteristics

#### Encapsulation
Does the component hide its implementation details or expose them in some way?

Does the component have external dependencies that are not bundled with the component?

#### Flexibility
Can the component be reused in different ways and in different places?

Can the theme be customized?

Does the component support dependency injection?

Flexibility makes a component more powerful but often comes at the cost of additional complexity.

#### Simplicity


#### Consistency
Are property names consistent?


How about across the whole system?

#### Ergonomics

Do you have to constantly reference the documentation or can you intuit other properties of the API?



### API surface area

A component exposes a certain amount of API surface area.

The more surface area is exposed, the more complex the API becomes and consequently the more testing is required.

Finding the right balance is difficult.


```jsx
<Tabs />
```

```jsx
<Tabs items={[
  {label: 'One'},
  {label: 'Two'},
  {label: 'Three'}
]} />
```

```jsx
<Tabs>
  <Tab>One</Tab>
  <Tab>Two</Tab>
  <Tab>Three</Tab>
</Tabs>
```




 -->
