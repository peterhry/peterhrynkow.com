---
layout: post
title: "The Elements of Component Design"
date: 2019-01-26 00:00:00
categories: [api]
---

Should you use CSS modules or styled components, React or Polymer? The _how_ of building components is something engineers spend a lot of time thinking about. After all, decisions around component _implementation_ are largely driven by factors like performance and developer experience — the sort of things engineers love to debate.

Less time is spent thinking about component _design_ — the _why_ and _what_ of building components. Just as there are many ways to implement a component, there are many ways to design its API. Whether you're building an application or component library, the decisions you make with regards to component design can greatly impact the success of your project.

After working on two major component library projects — one built in Polymer, the other in React — I’ve come to understand the importance of deliberate and thoughtful component design. 

Consistent vs flexible, abstract vs concrete — it can be a challenge to balance these dichotomies when designing your components.

People within your organization are likely to have different opinions about their priority.

### Consistent vs Flexible

Should the component allow the consumer to override its appearance and behaviour? If so, to what degree? Every team I've been on has struggled with this question.

Should the component allow its internal elements to be styled directly or limit the consumer to choosing a predefined theme (i.e. light, dark)?

TBC

### Abstract vs Concrete

Is it better to have one component that does five things or five components that do one thing?

Let me give you a specific example. Is it better to have one universal video player component or one for YouTube, Vimeo, Twitch, etc.?

TBC 

### Static vs Dynamic

TBC

### Versioned vs Evergreen

When distributing components, it's easy to introduce new features but much harder to take them away.

TBC

### Boolean vs Enum

TBC

### Props vs Children 

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




