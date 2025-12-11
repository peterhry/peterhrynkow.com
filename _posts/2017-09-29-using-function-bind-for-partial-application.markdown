---
layout: post
title:  "Partial Application in Action"
date:   2017-09-29 00:00:00
categories: [functional-programming]
---

Have you ever called the same function with the same argument over and over again? There’s a simple technique that reduces this repetition: [partial application](https://en.wikipedia.org/wiki/Partial_application).

Say you have a function that adds two numbers:

```js
const add = (a, b) => a + b

add(2, 4)
// 6
```

`Function.prototype.bind` lets you partially apply a function by fixing one or more arguments.

For example, if you partially apply `add` and fix the first argument with `10`, you get a new function `addTen` that adds `10` to whatever you pass in.

```js
const add = (a, b) => a + b
const addTen = add.bind(null, 10)

addTen(3)
// 13

addTen(6)
// 16
```

That’s a common demo, but here’s a practical use.

### Reducing argument repetition

If you want to fetch several resources from an API, your code might look like this:

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

Notice that `getData` and the first argument `apiBaseUrl` repeat. You can partially apply `getData` to remove that duplication.

```js
const apiBaseUrl = 'https://api.myapp.com/api/v1'
const getData = (baseUrl, resource) => fetch(`${baseUrl}/${resource}`)
const getDataFromAPI = getData.bind(null, apiBaseUrl)

Promise.all([
  getDataFromAPI('products'),
  getDataFromAPI('categories'),
  getDataFromAPI('tags'),
]).then((responses) => {
  // Do stuff
})
```

That’s better: the base URL is declared once, and every call shares it.

### Reducing function call repetition

Because `getDataFromAPI` now takes a single argument, you can pass it directly to `map`.

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

No more repetition—and the intent is clearer. Partial application is a tiny tool, but once you start using it your code often becomes more expressive and easier to refactor.
