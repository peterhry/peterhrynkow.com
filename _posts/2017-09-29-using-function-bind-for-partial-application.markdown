---
layout: post
title:  "Using `Function.bind` for partial application"
date:   2017-09-29 00:00:00
categories: [functional-programming]
---

Here’s a handy functional programming technique I’ve been using lately. Say we have a function that adds two numbers:
```js
function add(a, b) {
  return a + b;
}

add(2, 4);
// 6
```

With `Function.bind` we can create a new function from `add` that already has the first parameter applied. This is called [partial application](https://en.wikipedia.org/wiki/Partial_application) and is a process often-used in functional programming.

```js
const addTen = add.bind(null, 10);

addTen(3);
// 13

addTen(6);
// 16
```

If you’re wondering how this is useful, consider the following `forEach` loop:

```js
const numbers = [1, 2, 3, 4, 5];

numbers.forEach((number, index) => {
  numbers[index] = add(number, 10);
});
// [11, 12, 13, 14, 15]
```

While there’s nothing _wrong_ with the code above, I think we can make it better. By using partial application and `map` instead of `forEach` the code is shorter and in my opinion easier to follow.

```js
const numbers = [1, 2, 3, 4, 5];
const addTen = add.bind(null, 10);
const numbersPlusTen = numbers.map(addTen);
// [11, 12, 13, 14, 15]
```
