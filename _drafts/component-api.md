Much has been written about how to _implement_ components on the web— how to build feature X using technology Y — but considerably less has been written about component API design.

Component API design largely transcends technology. In many cases, you can design the API for a component before selecting a technology to implement it.

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




