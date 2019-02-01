---
layout: post
title: "The Elements of Component API Design"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components, React or Polymer? The _how_ of building UI components is something engineers seem to spend a lot of time thinking about. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to geek out on.

Just as there are many ways to _implement_ a component, there are many ways to _design its API_, and yet, engineers seem to invest less time in this area. Having worked on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful API design.

The decisions you make with regards to API design are likely to have a lasting impact on the success and adoption of your component library. A well designed API provides useful abstractions, is simple yet powerful, well-documented yet intuitive, and in general empowers the user to get things done. In contrast, a poorly designed API is complicated, intimidating, and unpredictable.

<!-- ![BMW Interior]({{ site.baseurl }}/images/bmw-interior.jpg)

Which of these interfaces would you want to use?

![Tesla Interior]({{ site.baseurl }}/images/tesla-interior.jpg)
 -->
Component API design is not without its challenges, though. In this post, I will share my experience with solving some common problems that teams face when designing component APIs.

### What is a Component API?

For a component to be useful, it needs to expose an API. The API allows the consumer or user to control and interact with the component. In React, it includes the component's props, children, callbacks, and rendered output. In Polymer, it includes other things like instance methods and events but I won't cover those here.

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

Callback functions and their signatures, supplied as props.

```jsx
const onClick = (event) => {
  // Do something
}

<Button onClick={onClick}>Submit</Button>
```


#### Rendered Output

Anything rendered by the component. In some cases this includes its styles.

### API Design Problems

#### Customization

It’s no secret that component-based design systems improve user experience by increasing visual and functional consistency. That being said, users often want to customize the appearance of components. How do you reconcile these opposing ideas?

Should a component enforce visual consistency by restricting access to its template and styles, or is it the role of people in the organization to police consistency?

There right answer to this question comes down to whether your component library prioritizes consistency or flexibility. 

If customization is supported, to what degree should a component allow the user to override its appearance?

Should a component allow its internal elements to be styled directly or only allow the user to choose a predefined theme (i.e. light, dark)?

Should a component allow any part of its template to be customized?

TBC

#### Abstractness

Is it better to have one component that does five things or five components that each do one thing?

For example, you could create one video component that supports multiple video providers (i.e. YouTube, Vimeo, Twitch) or multiple video components that each support one provider.

Engineers gravitate toward higher levels of abstractions because they reduce the amount of code repetition. But abstraction comes at a cost.

TBC

#### Composition

How should your component API handle composition?

Using an array or object prop to perform composition is one approach:

```jsx
<Tabs items={[
  {label: 'One'},
  {label: 'Two'},
  {label: 'Three'}
]} />
```

It might seem natural to pass an `array` or `object` to your component if your data is already in this format, but this approach has several drawbacks:

1. The user has no control over how each child component is created so there is no opportunity to specify its type and props.
1. Documenting the required data structure, its properties, and shape can be cumbersome.
1. It's unorthodox since no native HTML element receives data in this way.

https://twitter.com/brad_frost/status/1090733766950223878?s=21

Using the `children` prop to perform composition is idiomatic in React.

```jsx
<Tabs>
  <Tab>One</Tab>
  <Tab>Two</Tab>
  <Tab>Three</Tab>
</Tabs>
```

This pattern _lifts_ the responsibility of creating each child component, thus allowing the user to specify the type of child and its props.

One thing to note about this approach, is that it gives the impression that the component will slot the children into its template as is, but that's not always the case.

A component can iterate over its children and render different components in their place:

// example TBD


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

Composition using a render prop gives the most control to the user.

TBC


#### Consistency

- large teams?

#### Static vs Dynamic

<!--
Do you even need a client-side library to render your component?

If your components are used to render static documents like blog posts or documentation pages, you might not need a client-side component library.
-->

TBC


#### Versioning
<!--
When distributing your component library, it's easy to introduce a new feature but much harder to remove it once consumers depend on it.-->

TBC

<!--
### Boolean vs Enum

TBC

Does the component support dependency injection?

#### Consistency
Are property names consistent?

How about across the whole system?

#### Ergonomics

Do you have to constantly reference the documentation or can you intuit other properties of the API?



### API surface area

A component exposes a certain amount of API surface area.

The more surface area is exposed, the more complex the API becomes and consequently the more testing is required.

Finding the right balance is difficult.


 -->
