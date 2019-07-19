---
layout: post
title:  "Partial Application in Action"
date:   2017-09-29 00:00:00
categories: [functional-programming]
---

Have you ever found yourself calling the same function with the same argument, over and over again? Well, I have some good news for you, there's a simple technique that can help you reduce this kind of repetition. it's called [partial application](https://en.wikipedia.org/wiki/Partial_application). Say you have a function that adds two numbers:

```js
const add = (a, b) => a + b

add(2, 4)
// 6
```

`Function.prototype.bind` allows you to partially apply a function by passing it the arguments you want to fix.

For example, if you partially apply `add`,  fixing the first argument with a value of `10`, the result is a new function `addTen` that adds `10` to the remaining argument.

```js
const add = (a, b) => a + b
const addTen = add.bind(null, 10)

addTen(3)
// 13

addTen(6)
// 16
```

This is a common example of partial application but it’s not that useful in the real world. Here is a more practical example:

### Reducing argument repetition 

Say you want to fetch three different resources from an API, your code might look something like this:

```js
const apiBaseUrl = 'https://api.myapp.com/api/v1'
const getData = (baseUrl, resource) => fetch(`${baseUrl}/${resource}`)

Promise.all([
  getData(apiBaseUrl, 'products'),
  getData(apiBaseUrl, 'categories'),
  getData(apiBaseUrl, 'tags'),
]).then((responses) => {
  // Do stuff
})
```

Notice that `getData` and the first argument `apiBaseUrl` are repeated. You can use partial application to remove this repetition. 

If you partially apply `getData`, fixing the first argument with the value of `apiBaseUrl`, the result is a new function that takes the remaining argument `resource`:

```js
const apiBaseUrl = 'https://api.myapp.com/api/v1'
const getData = (baseUrl, resource) => fetch(`${baseUrl}/${resource}`)
const getDataFromAPI = getData.bind(null, apiBaseUrl)

Promise.all([
  ('products'),
  getDataFromAPI('categories'),
  getDataFromAPI('tags'),
]).then((responses) => {
  // Do stuff
})
```

That's a bit better. `apiBaseUrl` is no longer repeated but you can take it a step further by removing the repeated calls to `getDataFromAPI`.

### Reducing function call repetition 

By using partial application, you created a new function `getDataFromAPI` that takes a single argument. This allows you to pass it to the `map` function of an array, like so:

```js
const apiBaseUrl = 'https://api.myapp.com/api/v1'
const getData = (baseUrl, path) => fetch(`${baseUrl}/${path}`)
const getDataFromAPI = getData.bind(null, apiBaseUrl)

Promise.all(['products', 'categories', 'tags'].map(getDataFromAPI)).then(
  (responses) => {
    // Do stuff
  }
)
```

Now there is no repetition. The code is concise but is it more readable? Let me know what you think.

