---
layout: post
title:  "Practical partial application"
date:   2017-09-29 00:00:00
categories: [functional-programming]
---

Here’s a handy functional programming technique I’ve been using lately. Say you have a function that adds two numbers:
```js
const add = (a, b) => a + b

add(2, 4)
// 6
```

With `Function.bind` you can create a new function from `add` that already has the first argument applied. This technique is called [partial application](https://en.wikipedia.org/wiki/Partial_application) and is used often in functional programming.

```js
const add = (a, b) => a + b
const addTen = add.bind(null, 10)

addTen(3)
// 13

addTen(6)
// 16
```

If you’re wondering why this is useful, consider these examples:

### Avoiding mutation

Say you want to take an array of numbers and produce a new array where each element has `10` added to it. One approach would be to use `forEach` like so:

```js
const add = (a, b) => a + b
const numbers = [1, 2, 3, 4, 5]
numbersPlusTen = []

numbers.forEach((number) => {
  numbersPlusTen.push(add(number, 10))
});

console.log(numbersPlusTen)
// [11, 12, 13, 14, 15]
```

The above code works but it mutates the `numbersPlusTen` array with each iteration. Not the end of the world but you could make it more concise and functional by using partial application.

```js
const add = (a, b) => a + b
const addTen = add.bind(null, 10)
const numbers = [1, 2, 3, 4, 5]
const numbersPlusTen = numbers.map(addTen)
console.log(numbersPlusTen)
// [11, 12, 13, 14, 15]
```

Since the new function `addTen` expects a single argument, you can pass it directly to the `map` function. The result is a new array where each element has `10` added to it.


### Avoiding argument repetition

Here’s another situation where partial application comes in handy. Say you want to fetch three different resources from an API, you could write some code like this:

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

By partially applying `getData`, you can avoid repeating the `apiBaseUrl` for each call and your code gets a little more concise.

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

