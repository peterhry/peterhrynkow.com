---
layout: post
title:  "The Elements of Component API Design"
date:   2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components? Stick with React or [#usetheplatform](https://twitter.com/polymer)? Much has been written on the subject of implementing components — how to build feature X using technology Y. This is the _how_ of building components and it's what engineers tend to get most excited about. After all, decisions around component implementation are largely driven by requirements like performance, reliability, and even developer experience — the sort of things engineers love to argue about.

Less has been written about component API design — the _what_ and _why_ of building components — which is strange because it’s actually pretty darn important. Just as there are many ways to _implement_ a component, there are many ways to _design its interface_, and the decisions you make have the potential to impact the success of your project.

After working on two different component library projects — one in Polymer and one in React — I’ve become aware of what works and doesn’t work when it comes to component API design. This post is my attempt to describe the forces that come into play when designing component APIs and to help you avoid some of the pitfalls I’ve encountered.



### What is a Component API?

For a component to be useful it needs to expose an API. The API allows the consumer — that’s you — to control and interact with the component. The API can have inputs — props, children, and methods — and outputs — rendered UI, callbacks, and side effects.


#### Props or attributes
What props or attributes does the component accept? What types?

#### Children
What children does the component accept? What types, how many? Render props?

#### Instance methods
Does the component instance expose public methods? This is common for web components

#### Callbacks or events
What props or attributes does the component accept? What types?

#### Rendered UI
What UI elements can the user interact with? Buttons, text inputs, drop down menus — these are all part of the component's API.



There are often diametrically opposed ideas of what a consumer should be allowed to do with a component.




Some designers might want to ensure that whenever a component is used, its design and experience is consistent. While other designers


A
What are the guiding principals behind the API design?


#### Styling
Should the component API expose options for styling and customization or should it limit the consumer to choosing a predefined theme (light, dark, etc.)?

Perhaps the purpose of the component library is to ensure design consistency.


Component API design largely transcends technology. In many cases, you can design the API for a component before selecting a language and technology to implement it.




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




