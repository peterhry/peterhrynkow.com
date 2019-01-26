What are the elements of a component's API:

1. Props or attributes
What props or attributes does the component accept?

1. Children
What children does the component accept? What types, how many? Render props?

1. Instance methods 
Does the component instance expose public methods? It's unusual — maybe even impossible — for a React component to expose instance methods. This is common for web components.

1. UI inputs
What UI elements can the user interact with? Buttons, text inputs, drop down menus — these are all part of the component's API.

A lot has been written about how to implement components — how to build feature X using technology Y — but considerably less has been written about component API design. 

Component API design transcends technology. In many cases, you can design the API for a component before selecting a technology.

Encapsulation
Does the component expose its implementation details or have hidden dependencies?

Flexibility

Simplicity 

Consistency
Are property names consistent? How about across the whole system?

Ergonomics




# API surface area

A component exposes a certain amount of API surface area. 

The more surface area is exposed, the more complex the API becomes and consequently the more testing is required.

Finding the right balance is difficult.






