---
layout: post
title: "The Elements of Component API Design"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components, React or Polymer? The _how_ of building UI components is something engineers spend a lot of time thinking about. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to optimize.

Just as there are many ways to _implement_ a component, there are many ways to _design its API_, and yet, engineers seem to invest less time in this area. Having worked on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful API design.

Whether you're building an application or component-based design system, the decisions you make with regards to API design can have a lasting impact on your project. A thoughtfully designed API provides useful abstractions, is simple yet powerful, well-documented yet intuitive, and most importantly, helps the consumer achieve some goal. Component API design is not without it's challenges, though.

In this post, I will highlight some common problems that engineers face when designing component-based design systems, and share my experience on how to solve them.

### Customization

Component-based design systems improve user experience by allowing design patterns and brand elements to be shared and reused across many sites.

It can be difficult to reconcile ideas like consistency and customization. For instance, how do you a design a component API for a brand team that wants visual consistency and a project owner who wants customization?

Should the component library enforce visual consistency or is it the role of humans within the company to enforce this?

Should the component allow the consumer to override its appearance and/or behaviour? If so, to what degree?

Should the component allow its internal elements to be styled directly or limit the consumer to choosing a predefined theme (i.e. light, dark)?

Should the component allow any part of its template to be customized?

TBC

### Abstractness

Is it better to have one component that does five things or five components that each do one thing? 

For example, you could create one video component that supports multiple video providers (i.e. YouTube, Vimeo, Twitch) or multiple video components that each support one provider.

Engineers often gravitate toward higher levels of abstractions because they allow repeated code to be combined. This reduces file size as well as testing and maintenance effort, but abstraction comes at a cost — especially if done incorrectly.

TBC

### Composition

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
<Tabs renderItems={(selected) =>
  items.map(item =>
    <CoolTab selected={item.id === selected}>{item.label}</CoolTab>
  )
} />
```

TBC

### Static vs Dynamic

Do you even need a client-side library to render your component?

If your components are used to render static documents like blog posts or documentation pages, you might not need a client-side component library.


### Versioning

When distributing your component library, it's easy to introduce new features but much harder to remove then once consumers depend on them.



TBC


<!--
### Boolean vs Enum

TBC


### What is a Component API?

For a component to be useful, it needs to expose an API. The API allows the consumer to control and interact with the component. The API has inputs — props, children, events, and methods — and outputs — rendered UI, events, callbacks, and side effects.


#### Props or attributes
What props or attributes does the component accept? What types?

#### Children
What children does the component accept? What types, how many, does order matter?

#### Instance methods
Does the component expose instance methods? This is idiomatic for web components but unheard of in React.

#### Callbacks or events

#### Rendered UI
What UI elements can the user interact with? Buttons, text inputs, drop down menus — these are all part of the component's API.

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
