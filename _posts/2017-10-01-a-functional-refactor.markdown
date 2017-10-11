---
layout: post
title:  "A Functional Refactor"
date:   2017-10-01 00:00:00
categories: [functional-programming]
---

A few years ago, a colleague introduced me to something called a “pure function”, and so began my journey into the world of functional programming. Along the way, I discovered how to write software that is easier to reason about, easier to test, and more bulletproof.

In this post I’m going to show you some lessons I’ve learned and how you can refactor your JavaScript to be more functional.

### 1. Pure functions
A pure function is one which given the same input will always return the same output and has no observable side effect. Let’s look at some _impure_ functions and see how we can make them pure.

#### Don’t mutate arguments

Here we have a function that takes an array of numbers and adds `10` to each value. This function is impure because it has a side effect: It mutates the `numbers` array that we pass to it.

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

To make this function pure we need to return a new array, instead of mutating the original. This is where `Array.map` comes in handy. It creates a new array with the results of calling a function on every element in the calling array.


```js
const addTen = numbers => numbers.map(number => number + 10);
const numbers = [1, 2, 3, 4];
const numbersPlusTen = addTen(numbers);

console.log(numbers);
// [1, 2, 3, 4]
console.log(numbersPlusTen);
// [11, 12, 13, 14]
```


#### Declare all inputs as arguments

This function is impure because it accesses the variable `prefix` outside of its scope. The return value of a pure function should be determined only by its input values. Notice how we are able to affect the return value of `greet` by changing the value of `prefix`.

```js
let prefix = 'Hello';

function greet(name) {
  return `${prefix}, ${name}!`;
}

greet('Jane');
// Hello, Jane!

prefix = 'Bonjour';

greet('Jane');
// Bonjour, Jane!
```

To make the function pure, we need to pass `prefix` as an argument.

```js
function greet(prefix, name) {
  return `${prefix}, ${name}!`;
}

greet('Hello', 'Jane');
// Hello, Jane!

greet('Bonjour', 'Jane');
// Bonjour, Jane!
```


#### Given the same input, always return the same output

TBC

```js
function randomNumber(min, max) {
  return min + (max - min) * Math.random();
}
```

TBC


### 2. Avoid control structures

This function is pure but written in an imperative style. Functional programming avoids control structures such as loops and conditional statements.

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
const sum = numbers => numbers.reduce((total, number) => total + number);
```


### 3. Push side effects to the edges

Here we have a simple application where clicking a button updates a counter. The `incrementBy` and `addTen` functions are impure because they modify a variable outside of their scope.

This program is simple enough that we can tell what happens when the button is clicked, but in a more complex application, impure functions and their side effects make it difficult to keep track of changes to state.


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

Notice what happens when we refactor the application to use pure functions. We push the side effects to the edges. Now, `this.counter` is only changed in one place and the code is a little easier to reason about.

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

<!--
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">A functional style pushes side effects to the edges: &quot;gather information, make decisions, act.&quot;<br>A good plan in most life situations too.</p>&mdash; Jessica Kerr (@jessitron) <a href="https://twitter.com/jessitron/status/713432439746654209?ref_src=twsrc%5Etfw">March 25, 2016</a></blockquote>

This tweet hits the nail on the head. How does this apply to the refactored code?

1. Gather information (click events, current state)
2. Make decisions (determine new state from pure functions)
3. Act (assign new state)

For a program to be useful, we can’t eliminate side effects entirely.
 -->

