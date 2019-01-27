Should you use CSS modules or styled components, #usetheplatform or stick with React. Much has been written about _implementing_ components on the web — how to build feature X using technology Y. This is the _how_ of building components and it's what engineers tend to get most excited about. After all, component implementation is largely driven by requirements like performance, reliability, and responsiveness — the sort of things engineers love to tinker with.

Less has been written about component API design — the _what_ and _why_ of building components — which is strange because I'd argue it's more important. 

The API is how you as the consumer talk to your component and tell it what to do.

Component API design largely transcends technology. In many cases, you can design the API for a component before selecting a language and technology to implement it.

The design of a component's API has huge implications for the consumer: 

A well designed API is intuitive.

A well designed API hides complexity without restricting the consumer from achieving their goal.

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


### Elements of a Component API

What are the elements of a component's API:

#### Props or attributes
What props or attributes does the component accept? What types?

#### Children
What children does the component accept? What types, how many? Render props?

#### Instance methods
Does the component instance expose public methods? This is common for web components

#### UI
What UI elements can the user interact with? Buttons, text inputs, drop down menus — these are all part of the component's API.



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




