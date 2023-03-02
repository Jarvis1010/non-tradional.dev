---
title: "Functional Programing For Your Everyday JavaScript Developer"
datePublished: Wed Aug 26 2020 16:39:58 GMT+0000 (Coordinated Universal Time)
cuid: ckxnuw2dx09zh71s16dgfdtsi
slug: functional-programing-for-your-everyday-javascript-developer-f5efad54397d
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316449803/2tBQICWKZq.jpeg
tags: javascript, functional-programming

---

Photo by [Taras Shypka](https://unsplash.com/@bugsster?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

JavaScript has a pretty diverse set of developers. One of the biggest reasons is that JavaScript supports multiple programming paradigms. JavaScript can be written imperatively, you can use an object-oriented approach, and it can be written in a functional style.

Each of these paradigms is beneficial and helps you write better code, but they are very different in the way they choose to solve problems. There is no “superior” way to program, but instead one should learn all three styles of programming to help you have more solutions to solve problems.

Of the three styles, I believe functional-style programming has the largest barrier to learning it. This is even though anyone who has written and non-trivial JavaScript code, probably has used functional style. I believe this is not that functional style is hard to understand. It has more to do with the unfamiliar terminology and emphasis on “theoretical” definitions and examples. And as much as many of us find it interesting, there are not enough “practical” examples that help you get started.

What I will attempt to do in this post is to introduce you to functional programming in a non-jargony and practical way that you can start using in your codebase today.

### What is Functional Programming

First of all, what is functional programming and how is it different? The best way I can think of this is that, just like Object Oriented programming uses an object as it’s primary (or only) form of organization, functional programming emphasizes functions in that same manner.

There are a few important attributes of a functional style programming that is generally considered universal:

#### Functions are considered “first-class citizens”

When one says that functions are “first-class citizens” they are referring to the ability to pass functions around as values. In JavaScript and any other languages that support functional style paradigms, functions can be assigned to variables, passed into functions as arguments, and returned from functions as return values. For example:

```javascript
const outsideFn = () => "I was called";

function caller(fn) {
  return () => fn();
}

const newFn = caller(outsideFn);

newFn(); //'I was called'
```

#### All functions are “pure” and single purpose that can be “composed” together

When a function is said to be pure, it means that it takes some values and returns a value and leaves no side effects. This means that values should not be added or changed on the global scope. For example:

```javascript
//not pure function
let customer = null;
function getCustomerById(id) {
  customer = fetchCustomer({ id });
}

getCustomerById("12345");

//pure function
let customer = null;
function getCustomerById(id) {
  return fetchCustomer({ id });
}

customer = getCustomerById("12345");
```

It also means that objects and arrays should not be mutated or changed in a function, which leads me to the final point:

#### Data is “Immutable” and you create new data through “data transformation”

Data should be treated as if you can’t change it. In place of mutating data, one should create new data by transforming from the original. In JavaScript, primitive values such as strings, numbers, and booleans, are already immutable. `13+1` no matter how many times it is called, will not change the value of `1` nor `13.`

Objects and arrays, on the other hand, are mutable, which means you can change the value of properties. Many of the array functions that we know and love, mutate the array. Luckily, new language features have been added to JavaScript that allow us to mitigate this. For example:

```javascript
let arr = [];
let obj = {};

//instead of this
arr.push(1);

// do this
arr.concat(1);

// or even better
[...arr, 1]

//instead of this
obj.age = 12

//do this
Object.assign({}, obj, { age: 12 })

// or even better
{...obj, age: 12}
```

### Why is Functional Style Programming Beneficial

Let’s look at an arbitrary example of how to generate the classic song, ‘100 bottles of beer on the wall’.

First, the imperative way:

```javascript
for (let i = 0; i < 100; i++) {
  const lyric = `${i + 1} bottle${i > 0 ? "s" : ""} of beer on the wall, ${
    i + 1
  } bottle${i > 0 ? "s" : ""} of beer. Take one down, pass it around ${
    i > 0 ? i - 1 : "no more"
  } bottle${(i > 1) | (i === 0) ? "s" : ""} of beer on the wall.`;

  console.log(lyric);
}
```

On the other hand, a functional approach could be something like this:

```javascript
const beerLyrics = pipe(generateArray, generateLineOne, generateLineTwo, log);

beerLyrics(100);
```

When compared to an imperative approach, one of the biggest benefits of programming in a functional style is that you can break up your code into simple declarative steps. Since each step is handled by a single purpose, pure function, it can be swapped out or augmented with additional functions.

### Practical Functional Patterns

This is all well and good, but what can I start doing today in my real-life production app to take advantage of this functional style of programming? One of the easiest things you can do is to stop mutating objects and arrays. By avoiding mutating objects, even in seemingly trivial parts of our code, you can avoid some annoying bugs when the same object is mutated by another part of the code. It allows you to treat each object as unique because it is.

Some libraries can help, like immutable.js and immer.js. Ultimately, self-discipline is more than enough in most projects. You can also take advantage of `Object.freeze` to freeze existing objects as well as JavaScript getters to create read-only properties on objects if you need to.

The other thing you can do is start breaking your code up into single-purpose functions. Some functional languages, like Elm, enforce the rule that all functions can only have one argument. Though I don’t necessarily think that you should do that in JS, more often than not, it does beg that question, if you need more than one argument, is your function doing too much. Can it be broken down into a couple of single-use functions that can be composed together?

Take advantage of the higher-order functions. I know I said I would avoid jargon, but this is one that you need to know. A function is considered a ‘higher-order’ function if it takes a function as a parameter, returns a function, or both. If you have used any of what I call, [the Holy Trinity of array methods,](https://medium.com/the-non-traditional-developer/map-filter-reduce-the-holy-trinity-of-array-methods-16ce3bdb69e2) then you have already used higher-order functions.

One easy higher-order pattern you can start using today is currying. Currying is when you keep returning single-argument functions until you have received all the arguments needed to execute. For example:

```javascript
const add = (a) => (b) => a + b;

add(1)(2); // 3
```

Why would you do this? How can that be beneficial? It comes down to partial application. Sometimes, your code only knows part of the information it needs to execute code, but the rest of the code needs to execute in a different scope. One solution is to create a variable in a shared scope, but that just pollutes the scope and probably breaks the rule of pure functions. Instead, you can create partially applied functions using currying:

```javascript
const add = (a) => (b) => a + b;

// .....

const add10 = add(10);

// ....

const finalNum = add10(3);
```

Functional style programming can seem like it is complicated. When you learn to apply its patterns, your codebase becomes much more consistent and easier to maintain.
