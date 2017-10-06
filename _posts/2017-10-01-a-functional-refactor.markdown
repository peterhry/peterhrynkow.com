---
layout: post
title:  "💎 A Functional Refactor"
date:   2017-10-01 00:00:00
categories: [functional-programming]
---

In this post I’m going to show you some examples of imperative JavaScript and how you can refactor them to be more functional. I prefer a functional style because it produces code that is easier to reason about, easier to test, and more elegant.

### Don’t mutate function arguments

Here we have a function that takes an array of numbers and adds `10` to each value. This function is said to be _impure_ because it has a side effect: It mutates the `numbers` array that we pass to it.

```js
function addTen(numbers) {
  for (var i = 0; i < numbers.length; i++) {
    numbers[i] += 10;
  }
}

const numbers = [1, 2, 3, 4];

addTen(numbers);
console.log(numbers);
// [11, 12, 13, 14]
```

To make this function _pure_ we need to return a new array, instead of mutating the original. This is where `Array.map` comes in handy. It creates a new array with the results of calling a function on every element in the calling array.


```js
function addTen(numbers) {
  return numbers.map(number => number + 10);
}

const numbers = [1, 2, 3, 4];
const numbersPlusTen = addTen(numbers);

console.log(numbers);
// [1, 2, 3, 4]
console.log(numbersPlusTen);
// [11, 12, 13, 14]
```

### Avoid control structures

This function is _pure_ but written in an imperative style. Functional programming avoids control structures such as loops and conditional statements.

```js
function sum(numbers) {
  let sum = 0;

  for (var i = 0; i < numbers.length; i++) {
    sum += numbers[i];
  }

  return sum;
}
```

Using `Array.reduce` is shorter and more elegant.

```js
function sum(numbers) {
  return numbers.reduce((total, number) => total + number);
}
```


### Push side effects to the edges

Here we have a simple application where clicking a button updates a counter. The `incrementBy` and `addTen` functions are _impure_ because they modify a variable outside of their scope.

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

This program is simple enough that we can tell what happens when the button is clicked, but in a more complex application, _impure_ functions and their side effects make it difficult to keep track of changes to state.

What strategy can we use to improve this?
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

By refactoring the application to use pure functions, we push the side effects to the edges. `this.counter` is now controlled by a single function.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">A functional style pushes side effects to the edges: &quot;gather information, make decisions, act.&quot;<br>A good plan in most life situations too.</p>&mdash; Jessica Kerr (@jessitron) <a href="https://twitter.com/jessitron/status/713432439746654209?ref_src=twsrc%5Etfw">March 25, 2016</a></blockquote>

This tweet hits the nail on the head. How does this apply to the refactored code?

1. Gather information (click events, current state)
2. Make decisions (determine new state from pure functions)
3. Act (assign new state)

For a program to be useful, we can’t eliminate side effects entirely.
