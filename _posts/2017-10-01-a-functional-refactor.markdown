---
layout: post
title:  "💎 A Functional Refactor"
date:   2017-10-01 00:00:00
categories: [functional-programming]
---

In this post I’m going to show you some examples of imperative JavaScript and how you can refactor it to be more functional. I prefer a functional style because it produces code that is easier to reason about, easier to test, and in my opinion more elegant.

### Using `map` instead of imperative loops

Here we have a function that takes an array of numbers and adds `10` to each value. This function is said to be _impure_ because it has a side-effect: It mutates the `numbers` array that we pass to it.

```js
function addTen(numbers) {
  for (var i = 0; i < numbers.length; i++) {
    numbers[i] += 10;
  }

  return numbers;
}

const numbers = [1, 2, 3, 4];

addTen(numbers);
numbers;
// [11, 12, 13, 14]
```

To make this function _pure_ we need to return a new array, instead of mutating the original. This is where `map` comes in handy. It creates a new array with the results of calling a function on every element in the calling array.


```js
function addTen(numbers) {
  return numbers.map(number => number + 10);
}

const numbers = [1, 2, 3, 4];
const numbersPlusTen = addTen(numbers);
numbers;
// [1, 2, 3, 4]
numbersPlusTen;
// [11, 12, 13, 14]
```

### Using `reduce` to sum an array of numbers

This function is pure but written in an imperative style.

```js
function sum(numbers) {
  let sum = 0;

  for (var i = 0; i < numbers.length; i++) {
    sum += numbers[i];
  }

  return sum;
}
```

The functional version is shorter and more elegant.

```js
function sum(numbers) {
  return numbers.reduce((total, number) => total + number);
}
```


### Push side effects to the edges

> A functional style pushes side effects to the edges: “gather information, make decisions, act.” A good plan in most life situations too.
>
> – Jessica Kerr

Here we have a simple application where clicking a button increments a counter.

```js
class App {
  constructor(button) {
    this.counter = 0;

    button.addEventListener('click', () => {
      this.incrementBy(1);
      this.addTen();
    });
  }

  incrementBy(amount) {
    this.counter += amount;
  }

  addTen() {
    this.counter += 10;
  }
}
```

Let’s look at `incrementBy` and `addTen`. What do you notice about these functions?

1. They modify a variable `this.counter` outside of their scope.
2. They reference `this` which could be anything depending on how the function was called.

In a simple program like this, we can easily tell what happens when the button is clicked. But a more complex application, _impure_ functions like these make it more difficult to keep track of our application state.

We can’t always eliminate side-effects but we can keep them quarantined:


```js
class App {
  constructor(button) {
    this.counter = 0;

    button.addEventListener('click', () => {
      this.counter = this.addTen(this.incrementBy(this.counter, 1));
    });
  }

  // Pure
  incrementBy(number, increment) {
    return number + increment;
  }

  // Pure
  addTen(number) {
    return number + 10;
  }
}
```
