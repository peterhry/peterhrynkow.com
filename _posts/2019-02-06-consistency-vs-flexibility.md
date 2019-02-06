---
layout: post
title: "Consistency vs Flexibility in Pattern Libraries"
date: 2019-02-06 00:00:00
categories: [api]
---

Pattern libraries, component-based design systems — whatever you want to call them — it’s no secret that they improve user experience by increasing visual and functional consistency. Most designers will tell you that consistency is a key design principle. But how far should you go to prevent users from changing the appearance and behavior of components in your design system?

Should a component enforce visual consistency by restricting access to its template and styles, or give the user options for overriding its appearance? Is it the role of technology (your components) or people within your organization to enforce consistency?

If you’ve ever worked on a pattern library project, you might have found yourself asking some of these questions. And they're good questions to ask, because the decisions you make, in this regard, will no doubt inform the design of your component APIs.

In this post I will discuss an area of component API design where the balance between consistency and flexibility comes into play. The approach you take will likely depend on the level of flexibility you intend to provide.

### Child Composition

In React, there are several ways to compose child elements. Some solutions provide the consumer with more flexibility than others. Let's look at a few options.

#### Array or object prop

Using an `array` or `object` prop to compose child elements is the least flexible option because the component controls what type of child is rendered and its props. This approach is suitable for component libraries that prioritize consistency over flexibility because the component controls all aspects of rendering.

Here the component is saying: **Just give me the data — I'll take care of rendering the children.**

```jsx
const Tab = ({children, selected, onClick}) => {
  const style = {
    background: selected ? '#fff' : '#ccc'
  }

  return (
    <li onClick={onClick} style={style}>
      {children}
    </li>
  )
}

class Tabs extends React.Component {
  constructor() {
    super()

    this.state = {
      selectedIndex: 0
    }
  }

  setSelectedIndex = (index) => {
    this.setState({selectedIndex: index})
  }

  render() {
    return (
      <ul>
        {this.props.items.map((item, index) => (
          <Tab
            key={index}
            onClick={() => this.setSelectedIndex(index)}
            selected={index === this.state.selectedIndex}
          >
            {item.label}
          </Tab>
        ))}
      </ul>
    )
  }
}

ReactDOM.render(
  <Tabs items={[{label: 'One'}, {label: 'Two'}, {label: 'Three'}]} />,
  document.getElementById('root')
)
```

[Live Demo](https://codepen.io/peterhry/pen/ErbYLL?editors=1010)

In this example, the consumer provides an array of items and the `Tabs` component decides what to render. This makes it difficult for the consumer to override the appearance and behavior of the tabs.

#### Children Prop

Using the `children` prop to compose child elements is idiomatic in React. This approach is a bit more flexible because it lets the consumer render the children. However, the result is a tight coupling between the component and its children. For the component to control its children, they must expose the necessary props.

Here the component is saying: **Give me the children and I'll slot them in somewhere. Just make sure they expose the required props so I can set them**.

```jsx
const FancyTab = ({children, selected, onClick}) => {
  const style = {
    background: selected ? '#0099ff' : '#ccc'
  }

  return (
    <li onClick={onClick} style={style}>
      {children}
    </li>
  )
}

class Tabs extends React.Component {
  constructor() {
    super()

    this.state = {
      selectedIndex: 0
    }
  }

  setSelectedIndex = (index) => {
    this.setState({selectedIndex: index})
  }

  render() {
    return (
      <ul>
        {this.props.children.map((child, index) =>
          React.cloneElement(child, {
            key: index,
            onClick: () => this.setSelectedIndex(index),
            selected: index === this.state.selectedIndex
          })
        )}
      </ul>
    )
  }
}

ReactDOM.render(
  <Tabs>
    <FancyTab>One</FancyTab>
    <FancyTab>Two</FancyTab>
    <FancyTab>Three</FancyTab>
  </Tabs>,
  document.getElementById('root')
)
```

[Live Demo](https://codepen.io/peterhry/pen/ErbYLL?editors=1010)


In this example, the consumer wants to use `FancyTab` components. The only reason this works is because `FancyTab` exposes the required props `selected` and `onClick`.

#### Render Prop

Using a render prop to compose child elements is an advanced pattern that delegates all aspects of rendering to the consumer. Furthermore, it allows the consumer to render children with an otherwise incompatible API. The result is a loose coupling between the component and its children.

Here the component is saying: **Render the children when I call this function. I'll expose an API to help you integrate your super-custom child components.**

```jsx
const HoverTab = ({children, active, onMouseEnter}) => {
  const style = {
    background: active ? '#fff' : '#ccc',
    fontStyle: active ? 'italic' : 'unset'
  }

  return (
    <li onMouseEnter={onMouseEnter} style={style}>
      {children}
    </li>
  )
}

class Tabs extends React.Component {
  constructor() {
    super()

    this.state = {
      selectedIndex: 0
    }
  }

  setSelectedIndex = (index) => {
    this.setState({selectedIndex: index})
  }

  render() {
    return (
      <ul>{this.props.children(this.setSelectedIndex, this.state.selectedIndex)}</ul>
    )
  }
}

const items = [{label: 'One'}, {label: 'Two'}, {label: 'Three'}]

ReactDOM.render(
  <Tabs>
    {(setSelectedIndex, selectedIndex) =>
      items.map((item, index) => (
        <HoverTab
          key={index}
          onMouseEnter={() => setSelectedIndex(index)}
          active={index === selectedIndex}
        >
          {item.label}
        </HoverTab>
      ))
    }
  </Tabs>,
  document.getElementById('root')
)
```

[Live Demo](https://codepen.io/peterhry/pen/ErbYLL?editors=1010)

In this example, the consumer wants to use custom `HoverTab` components, but there’s one problem: The `HoverTab` component exposes an `onMouseEnter` prop instead of `onClick` and an `active` prop instead of `selected`. The consumer needs a way to adapt one API to another. This is where render props come in handy.

When the `Tabs` component is rendering, it invokes the `children` prop (now a function) and passes it `selectedIndex` and `setSelectedIndex`. This allows the consumer to map the `HoverTab` props to the API provided by the `Tabs` component.

See also: [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control#Examples)

#### Which approach is best?

It depends whether your component library prioritizes consistency or flexibility. If you want to lock-down the design and behaviour of your components then an `array` prop might be best. If you want to provide your users with ultimate flexibility, then go for a render prop.
