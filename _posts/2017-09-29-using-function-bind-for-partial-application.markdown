---
layout: post
title:  "Practical Application in Action"
date:   2017-09-29 00:00:00
categories: [functional-programming]
---

Here’s a handy functional programming technique I’ve been using lately. Say you have a function that adds two numbers:
```js
const add = (a, b) => a + b

add(2, 4)
// 6
```

With `Function.bind` you can create a new function based on `add` that has the first argument `a` applied. The new function takes just one argument `b`. This technique is called [partial application](https://en.wikipedia.org/wiki/Partial_application).

```js
const add = (a, b) => a + b
const addTen = add.bind(null, 10)

addTen(3)
// 13

addTen(6)
// 16
```

### How is this useful?

Here’s a situation where partial application comes in handy. Say you want to fetch three different resources from an API, your code might look something like this:

```js
const apiBaseUrl = 'https://api.myapp.com/api/v1'
const getData = (baseUrl, path) => fetch(`${baseUrl}/${path}`)

Promise.all([
  getData(apiBaseUrl, 'products'),
  getData(apiBaseUrl, 'categories'),
  getData(apiBaseUrl, 'tags'),
]).then((responses) => {
  // Do stuff
})
```

By partially applying `getData`, you can avoid repeating `apiBaseUrl` and your code gets a little more concise.

```js
const apiBaseUrl = 'https://api.myapp.com/api/v1'
const getData = (baseUrl, path) => fetch(`${baseUrl}/${path}`)
const getDataFromAPI = getData.bind(null, apiBaseUrl)

Promise.all([
  getDataFromAPI('products'),
  getDataFromAPI('categories'),
  getDataFromAPI('tags'),
]).then((responses) => {
  // Do stuff
})
```

That looks better but you can take it a step further. Since `getDataFromAPI` takes a single argument, you can use it to map over an array of resources to fetch.

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

