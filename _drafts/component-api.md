---
layout: post
title:  "The Forces of Component Design"
date:   2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components, React or Polymer? The _how_ of building UI components is something engineers spend a lot of time thinking about. After all, decisions around component implementation are largely driven by requirements like performance and developer experience — the sort of things engineers love to debate.

Considerably less time is spent thinking about component _design_ — the _why_ and _what_ of building components. Component design largely transcends technology — in some cases, you can design the API for a component before selecting a technology for implementation. Just as there are many ways to _implement_ a component, there are many ways to _design its API_ and — whether you're building an application or component library — the decisions you make can greatly impact the success of your project.

After working on two major component library projects — one in Polymer and one in React — I’ve come to understand the importance of deliberate and thoughtful component design. People within an organization will have different goals in mind when starting a component library project and it's important to reconcile these different goals before writing any code.

In this post I will shed some light on these goals and how they can — for better or worse — impact the success of a component library.

#### Consistency/Uniformity

Some designers might want to ensure that whenever a component is used, its design and experience are consistent. While other designers might resist this idea and want more control.

Perhaps the purpose of the component library is to ensure design consistency.

#### Flexibility/Themability

Should the component API expose options for styling and customization or should it limit the consumer to choosing a predefined theme (light, dark, etc.)?

#### Idealism

#### Pragmatism

Is it better to have one component that does ten things or ten components that do one thing?

### What is a Component API?

For a component to be useful it needs to expose an API. The API allows the consumer to control and interact with the component. The API has inputs — props, children, events, and methods — and outputs — rendered UI, events, callbacks, and side effects.


#### Props or attributes
What props or attributes does the component accept? What types?

#### Children
What children does the component accept? What types, how many, does order matter?

#### Instance methods
Does the component expose instance methods? This is common and idiomatic for web components but unheard of in React.

#### Callbacks or events

#### Rendered UI
What UI elements can the user interact with? Buttons, text inputs, drop down menus — these are all part of the component's API.


When distributing versioned components, it's easy to introduce new features but much harder to take them away.







What are the guiding principals behind the API desig





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




