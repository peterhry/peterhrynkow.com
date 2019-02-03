---
layout: post
title: "Does Your Component Library Prioritize Consistency or Flexibility?"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use React or Polymer, CSS modules or styled components?  Engineers spend a lot of time
thinking about the _how_ of building components and  debating the merits of different tools and technologies. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to optimize.

Just as there are many ways to _implement_ a component, there are many ways to _design its API_. Having worked on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful API design.

The design of your API can have a lasting impact on the success and adoption of your component library. A well designed API is simple yet powerful, well-documented yet intuitive, and empowers the user to build interfaces quickly and with ease. In contrast, a poorly designed API is complicated, unintuitive, and frustrating.

> API design is hard because you can make it seem simple but it's actually deceptively complex, or make it actually simple but seem complex.
[sebmarkbage](https://twitter.com/sebmarkbage/status/728433349337841665)

As [@sebmarkbage](https://twitter.com/sebmarkbage) points out, API design is not without its challenges. In this post, I will share my thoughts on some common component API design problems. What I've found is that they often boil down to a single question: Does your component library prioritize flexibility or consistency?

But first, let’s go over the various parts of a component API.

### What is a Component API?

A component API is a contract between the component and its consumer that governs how the two parties communicate. In React, a component API includes:

#### The name and type of each prop

This `Button` component expects a `boolean` value for its `disabled` prop and a `string` value for its `type` prop.
```jsx
() => <Button disabled type="submit">Submit</Button>
```

#### The shape of object and array props

This `List` component expects its `items` prop to be an `array` where each element is an object with an `id: number` and `label: string` property.

```jsx
() => <List items={[
  {id: 1, label: 'One'},
  {id: 2, label: 'Two'}
]} />
```

#### The signature of function props

The consumer expects this component to call `onProgress` with `loaded: number` and `total: number`.

```jsx
const onProgress = (loaded, total) => {
  const percentage = loaded / total
}

() => <Loader onProgress={onProgress}></Loader>
```

#### The accepted type of children

This component expects to receive `Tab` components as children.

```jsx
() => <Tabs>
  <Tab id="1">One</Tab>
  <Tab id="2">Two</Tab>
  <Tab id="3">Three</Tab>
</Tabs>
```

### Customization

It’s no secret that component-based design systems improve user experience by increasing visual and functional consistency. That being said, users often want to customize the appearance and behaviour of components. How do you reconcile these two opposing ideas?

🧘‍♀️🗿

Should a component enforce visual consistency by restricting access to its template and styles, or give the consumer options for overriding its appearance?

Is it the role of technology or people to police consistency?

I think the answer to this question boils down to whether your component library prioritizes flexibility or customization.

If customization is supported, to what degree should a component allow the consumer to override its appearance?

Should a component allow its internal elements to be styled directly or only allow the consumer to choose a predefined theme (i.e. light, dark)?

TBC

### Abstractness

Is it better to have one component that does five things or five components that each do one thing?

For example, you could create one video component that supports multiple video providers (i.e. YouTube, Vimeo, Twitch) or multiple video components that each support one provider. The former provides a higher-level of abstraction because it hides away details about the video provider.

> We are providing low-level components to maximize composition capabilities.
https://material-ui.com/guides/api/

Low-level components afford more flexibility because the consumer can combine them in more ways.

Engineers gravitate toward higher levels of abstractions because they reduce the amount of code repetition.

TBC

### Composition

In React, there are several ways to compose child elements. Some solutions offer more flexibility than others.

This thread from Brad Frost highlights some of the trade offs between different approaches.

#### Array or object prop

Using an `array` or `object` prop to compose child elements might be suitable for component libraries that prioritize consistency over flexibility.

Here the component is saying “Just give me the data —I'll take care of rendering the children".

```jsx
() => <Tabs items={[
  {id: 1, label: 'One'},
  {id: 2, label: 'Two'},
  {id: 3, label: 'Three'}
]} />
```

Pros:
- Ensures that the correct type of child component is rendered.
- The consumer doesn't need to know what type of child component to supply.

Cons:
- The consumer has no control over child component being rendered so there is no opportunity to specify its type and props.
- Documenting the required data structure, its properties, and shape can be cumbersome.
- It's unorthodox since no native HTML element receives data this way.

#### Children Prop

Using the `children` prop to compose child elements is idiomatic in React. 

Here the component is saying “Give me the children and I'll slot them in somewhere".

```jsx
() => <Tabs>
  <Tab id="1">One</Tab>
  <Tab id="2">Two</Tab>
  <Tab id="3">Three</Tab>
</Tabs>
```

Pros:
- Allows the consumer to specify the type of each child component and its props.

Cons:
- The consumer might pass in the wrong type of component.

#### Render Prop

Using a render prop to manage composition is an advanced pattern that delegates all aspects of rendering to the consumer. Furthermore, it allows the consumer to receive information about the component's state before rendering its children.

Here the component is saying “Render the children when I tell you to — and by the way — here is some information about my state in case you need it."

```jsx
const items = [
  {id: 1, label: 'One'},
  {id: 2, label: 'Two'},
  {id: 3, label: 'Three'}
]

() => <Tabs renderItems={(selectedId) =>
  items.map(item =>
    <Tab key={item.id} selected={item.id === selectedId}>{item.label}</Tab>
  )
} />
```

What approach is best? Again, it depends whether your component library prioritizes consistency or flexibility. If you want to lock-down the design then maybe an `array` prop is best. If you want ultimate flexibility then go for a render prop.
