---
layout: post
title: "The Elements of Component API Design"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components, React or Polymer? The _how_ of building UI components is something engineers spend a lot of time thinking about. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to geek out on.

Just as there are many ways to _implement_ a component, there are many ways to _design its API_. Having worked on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful API design.

The decisions you make with respect to API design are likely to have a lasting impact on the success and adoption of your component library. A well designed API provides useful abstractions, is simple yet powerful, well-documented yet intuitive, and empowers the user to build interfaces quickly and with ease. In contrast, a poorly designed API is complicated, intimidating, and unpredictable.

<!-- ![BMW Interior]({{ site.baseurl }}/images/bmw-interior.jpg)

Which of these interfaces would you want to use?

![Tesla Interior]({{ site.baseurl }}/images/tesla-interior.jpg)
 -->
Component API design is not without its challenges, though. In this post, I will describe some common themes that impact API design and share some ideas for solving the problems they present.

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

Callback functions and their signatures, supplied as props.

```jsx
const onClick = (arg1, arg2) => {
  // Do something
}

<Button onClick={onClick}>Submit</Button>
```

#### Rendered Output

Anything rendered by the component. In some cases this includes its styles.

### API Design Themes

#### Customization

It’s no secret that component-based design systems improve user experience by increasing visual and functional consistency. That being said, users often want to customize the appearance of components. How do you reconcile these two opposing ideas?

Should a component enforce visual consistency by restricting access to its template and styles, or is it the role of people in the organization to police consistency?

The right answer to this question really comes down to whether your component library prioritizes consistency or customization. It's hard to have both.

If customization is supported, to what degree should a component allow the user to override its appearance?

Should a component allow its internal elements to be styled directly or only allow the user to choose a predefined theme (i.e. light, dark)?

Should a component allow any part of its template to be customized?

TBC

#### Abstractness

Is it better to have one component that does five things or five components that each do one thing?

For example, you could create one video component that supports multiple video providers (i.e. YouTube, Vimeo, Twitch) or multiple video components that each support one provider.

Engineers gravitate toward higher levels of abstractions because they reduce the amount of code repetition.

TBC

#### Composition

The solutions that exist for managing composition, delegate varying degrees of control to the consumer.

This thread from Brad Frost highlights some of the trade offs.

Using an `array` or `object` prop to manage composition is one approach. Here the component is saying "Please give me the data —I'll take care of rendering".

```jsx
<Tabs items={[
  {label: 'One'},
  {label: 'Two'},
  {label: 'Three'}
]} />
```

It might seem natural to pass an `array` or `object` to your component if your data is already in this format, but this method has several drawbacks:

1. The consumer has no control over how each child component is created so there is no opportunity to specify its type and props.
1. Documenting the required data structure, its properties, and shape can be cumbersome.
1. It's unorthodox since no native HTML element receives data in this way.

`children` prop 

Using the `children` prop to manage composition is idiomatic in React. Here the component is saying "Please give me the children and I'll slot them in somewhere".

```jsx
<Tabs>
  <Tab>One</Tab>
  <Tab>Two</Tab>
  <Tab>Three</Tab>
</Tabs>
```

This pattern is more flexible since it allows the consumer to specify the type of each child component and its props.


Render Prop

Using a render prop to manage composition is an advanced pattern that delegates all aspects of rendering to the consumer. Furthermore, it allows the consumer to receive information about the component's state before rendering the children.

Here the component is saying "Please render the children — and by the way — here is some information about my state in case you need it."

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

What approach is best? It depends. Does you component prioritize consistency or flexibility.

#### Consistency

- large teams?

#### Versioning
<!--
When distributing your component library, it's easy to introduce a new feature but much harder to remove it once consumers depend on it.-->

TBC
