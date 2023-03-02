---
title: "Modernizing Code Examples from Books: splat and unsplat"
datePublished: Wed Oct 13 2021 13:03:02 GMT+0000 (Coordinated Universal Time)
cuid: ckwokyx7v0fws2ds13gg8f3as
slug: modernizing-code-splat-and-unsplat-78aa9d0dd77f
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316745699/JtybighVP.jpeg
tags: javascript, typescript, functional-programming

---

Photo by [Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of my favorite books for JavaScript is “Functional JavaScript” by Michael Fogus. Even though it was written in JavaScript pre-ES2015 and leans heavily on underscore.js, a groundbreaking functional utility library that has since lost much of its market share, the core concepts taught in the book stand the test of time and show you how to think like a functional JavaScript Programmer.

That said, the examples are out of date. More and more new developers will have a more challenging time reading the code examples or even knowing how to apply the examples in a modern codebase, especially one written in TypeScript.

So I thought it would be a great exercise, to slowly update the examples here so that they show more modern styles of JavaScript while hopefully extending the life of this great resource that is available to the community.

(In this example, I will only be going over minimum amount of the content from the book as possible. I still highly recommend the book,, and I want to encourage you to buy it and use it as a resource.)

So the first example shown is splat and unsplat, two contrived utility functions:

```javascript
function splat(fun) {
  return function (array) {
    return fun.apply(null, array);
  };
}

var addArrayElements = splat(function (x, y) {
  return x + y;
});

addArrayElements([1, 2]);
//=> 3

function unsplat(fun) {
  return function () {
    return fun.call(null, _.toArray(arguments));
  };
}

var joinElements = unsplat(function (array) {
  return array.join(" ");
});

joinElements("-", "$", "/", "!", ":");
//=> '- $ / ! :'
```

To summarize the above, we are creating two utility functions: `splat` and `unsplat`. Both take a function as an argument and return a new function, a pattern known as higher-order functions.

`splat` takes a function and returns a new function that accepts an array of values. Each value will be applied as an argument to the provided function.

`unsplat,` takes an inverse approach to its `splat` counterpart. It takes a function that accepts an array as its argument and returns a function that will convert all the arguments passed into an array and apply that array to the provided function.

(Once again, for more details, please refer to the book.)

The first thing we can do is update the implementation of how `splat` and `unsplat` work under the hood. `splat` takes advantage of [the](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)`[apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)` [method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) provided that is part of all functions in JavaScript. Simply put, apply takes a context (which isn’t essential in this use case, hence why `null`is used) and an array of values. Each value in the array is applied as an argument to the function.

`unsplat` is similar, but it is implemented using [the](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) `[call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)` [method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) provided on all JavaScript functions. Like `apply,` it takes a context, but instead of an array of values, all the remaining arguments supplied to the `call` method will be passed as arguments to the function.

Using call and apply was great when that was the only thing you could do, but we now have access to the spread and rest operators in modern JavaScript. The spread operator will let you spread the values of an array out as arguments to any function. This means we can update our `splat` function to look like this:

```javascript
function splat(fun) {
  return function (array) {
    return fun(...array);
  };
}
```

The `unsplat` function can also be updated similarly. In the old example we were using [the](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) `[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)` [object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments), which is an object that looks like an array, but isn’t one. So it has to be converted to an array (the book’s example uses the underscore utility function to do that). Now that we have the rest operator we can bundle all the arguments into a single array, without needing to convert it, like this:

```javascript
function unsplat(fun) {
  return function (...args) {
    return fun(args);
  };
}
```

This is more readable and easy to understand, but we also don’t have to worry about the context, which was not necessary for what we were doing.

This is great already, but we are still only using one form of writing our functions. With ES2015 we also have an arrow function syntax that gives us more flexibility on how we write our functions. [I’ve given my opinions](https://non-traditional.dev/opinionated-javascript-4585b4b353e6) on JavaScript in an earlier post, but I never shared my opinions on function declarations.

I simply have two rules, named functions should prefer the traditional function declaration syntax and anonymous functions should prefer arrow function syntax. Also one should prefer the implicit return syntax if the return value is simple and easy to fit on one line. With that, we can update the entire file to look like this:

```jsx
function splat(fun) {
  return (array) => fun(...array);
}

const addArrayElements = splat((x, y) => x + y);

addArrayElements([1, 2]);
//=> 3

function unsplat(fun) {
  return (...args) => fun(args);
}

const joinElements = unsplat((array) => array.join(" "));

joinElements("-", "$", "/", "!", ":");
//=> '- $ / ! :'
```

Finally, let’s add some type safety to our new functions. I’ve already written a post about [Dynamic typing using Generics](https://non-traditional.dev/dynamic-typing-in-typescript-using-generics-f88fa52428cb), which luckily translates very well to these two functions. So let’s update them now to use Typescript:

```typescript
function splat<T extends Array<unknown>, R = unknown>(
  fun: (...arr: T) => R
): (arr: T) => R {
  return (array) => fun(...array);
}

const addArrayElements = splat((x: number, y: number) => x + y);

addArrayElements([1, 2]);
//=> 3

addArrayElements([1, 2, 3]); //TS Error
addArrayElements([1]); //TS Error
addArrayElements(["1", "2"]); //TS Error

function unsplat<T extends Array<unknown>, R = unknown>(
  fun: (arr: T) => R
): (...arr: T) => R {
  return (...args) => fun(args);
}

const joinElements = unsplat((array) => array.join(" "));

joinElements("-", "$", "/", "!", ":");
//=> '- $ / ! :'
```

There we have it. We have successfully updated the first code example to use a modern form of functional JavaScript. I will be sharing some more examples as I re-read the book from the beginning, but this should help you be able to look at code examples from older books and update them to modern JavaScript.
