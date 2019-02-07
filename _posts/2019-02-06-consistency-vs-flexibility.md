---
layout: post
title: "Flexibility in Component API Design"
date: 2019-02-06 00:00:00
categories: [api]
---

Pattern libraries, component-based design systems — whatever you want to call them — it’s no secret that they improve user experience by increasing visual and functional consistency. Most designers will tell you that consistency is an important design principle, so how much flexibility, if any, should be afforded to users who want to customize the components in your design system?

If you’ve ever worked on a pattern library project, you might have found yourself asking this question. And it's a good question to ask, because the decisions you make, in this regard, will no doubt inform the design of your component APIs.

In this post I will discuss an area of component API design where the balance between consistency and flexibility comes into play. The approach you take will likely depend on the level of flexibility you intend to provide.

### Child Composition

In React, there are several ways to compose child elements. Some solutions provide the consumer with more flexibility than others. Let's look at a few options.

#### Array or object prop

Flexibility: 🧘‍♂️

Using an `array` or `object` prop to compose child elements is the least flexible option because the component controls the type of children that are rendered and their props. This approach is suitable for component libraries that prioritize consistency over flexibility because the component controls all aspects of rendering.

Here the component is saying: **Just give me the data — I'll take care of rendering the children.**

<p
  class="codepen"
  data-height="416"
  data-theme-id="dark"
  data-default-tab="js"
  data-user="peterhry"
  data-slug-hash="ErbYLL"
  style="height: 416px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;"
  data-pen-title="React Child Composition Methods - Array Prop">
  <span>See the Pen <a href="https://codepen.io/peterhry/pen/ErbYLL/">
  React Child Composition Methods - Array Prop</a> by Peter Hrynkow (<a href="https://codepen.io/peterhry">@peterhry</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

In this example, the consumer provides an array of items and the `Tabs` component decides what to render. This makes it difficult for the consumer to override the appearance and behavior of the tabs.

#### Children Prop

Flexibility: 🧘‍♂️ 🧘‍♂️ 🧘‍♂️

Using the `children` prop to compose child elements is idiomatic in React. This approach is a bit more flexible because it lets the consumer render the children. However, the result is a tight coupling between the component and its children. For the component to control its children, they must expose the necessary props.

Here the component is saying: **Give me the children and I'll slot them in somewhere. Just make sure they expose the required props so I can set them**.

<p
  class="codepen"
  data-height="416"
  data-theme-id="dark"
  data-default-tab="js"
  data-user="peterhry"
  data-slug-hash="omowzK"
  style="height: 416px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;"
  data-pen-title="React Child Composition Methods - Children Prop">
  <span>See the Pen <a href="https://codepen.io/peterhry/pen/omowzK/">
  React Child Composition Methods - Children Prop</a> by Peter Hrynkow (<a href="https://codepen.io/peterhry">@peterhry</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

In this example, the consumer wants to use `FancyTab` components instead of `Tab` components. The only reason this works is because `FancyTab` exposes the required props `selected` and `onClick`.

#### Render Prop

Flexibility: 🧘‍♂️ 🧘‍♂️ 🧘‍♂️ 🧘‍♂️ 🧘‍♂️

Using a render prop to compose child elements is an advanced pattern that delegates all aspects of rendering to the consumer. Furthermore, it allows the consumer to render children with an otherwise incompatible API. The result is a loose coupling between the component and its children.

Here the component is saying: **Render the children when I call this function. I'll expose an API to help you integrate your super-custom child components.**

<p
  class="codepen"
  data-height="416"
  data-theme-id="dark"
  data-default-tab="js"
  data-user="peterhry"
  data-slug-hash="bzYRBW"
  style="height: 416px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;"
  data-pen-title="React Child Composition Methods - Render Prop">
  <span>See the Pen <a href="https://codepen.io/peterhry/pen/bzYRBW/">
  React Child Composition Methods - Render Prop</a> by Peter Hrynkow (<a href="https://codepen.io/peterhry">@peterhry</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

In this example, the consumer wants to use custom `HoverTab` components, but there’s one problem: The `HoverTab` component exposes an `onMouseEnter` prop instead of `onClick` and an `active` prop instead of `selected`. The consumer needs a way to adapt one API to another. **This is where render props come in handy.**

When the `Tabs` component is rendering, it invokes the `children` prop (now a function) and passes it `selectedIndex` and `setSelectedIndex`. This allows the consumer to map the `HoverTab` props to the API provided by the `Tabs` component.

See also: [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control#Examples)

#### Which approach is best?

It depends whether your component library prioritizes consistency or flexibility. If you want to lock-down the design and behaviour of your components then an `array` prop might be best. If you want to provide your users with ultimate flexibility, then go for a render prop.
