---
title: "TypeScript and Best Practices: Modernizing the nth Function in JavaScript"
datePublished: Fri Dec 03 2021 19:10:19 GMT+0000 (Coordinated Universal Time)
cuid: ckwqrhult01v924s17daa7929
slug: typescript-and-best-practices-modernizing-the-nth-function-in-javascript
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/SsBI9pweAeA/upload/v1638428473224/hvZ-FhpZM5.jpeg
tags: javascript, web-development, typescript, functional-programming

---

Are you looking to modernize your JavaScript code? Look no further than our posts on updating code examples from books. In this third installment, we’ll show you how to modernize the `nth` function from "Functional JavaScript." We'll walk you through updating the code, including adding types with TypeScript. Follow along and learn how to update your code with the latest best practices.

Here are the original examples:

```javascript
function isIndexed(data) {
  return _.isArray(data) || _.isString(data);
}

function nth(a, index) {
  if (!_.isNumber(index)) fail("Expected a number as the index");
  if (!isIndexed(a)) fail("Not supported on non-indexed type");
  if (index < 0 || index > a.length - 1) fail("Index value is out of bounds");

  return a[index];
}

function second(a) {
  return nth(a, 1);
}

second("abc"); // b
nth([1, 2, 3], 0); // 1
nth({}, 2); // Error: Not supported on non-indexed type
```

The `nth` function takes in an indexable value and an index. It does some guards to make sure you have actually passed in an indexable value and an index that is in bounds, and if everything is safe, it will return the value at the index. Since we already went over the `fail` function in [the previous post](https://non-traditional.dev/modernizing-code-examples-from-books-parseage-a6a4177d5b38), I have left that code out.

Let's start with the `isIndexed` function. This uses two utilities from underscore.js to confirm if the value is either an array or a string. There isn't an "array" type in JavaScript that you can check using the `typeof` operator. If you call `typeof []`, you will get "object." This is because arrays are just objects under the hood. In the past, we relied on utility functions, like `_.isArray` to reliably check if value actually is an array and not just an object.

Luckily, we have a static method on the `Array` object called `isArray` that does this without needing a utility library. You can [learn more about `Array.isArray` over at MDN.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray). Also, in the last post, we created our own `assertIsString` function. We can steal that logic and replace both of these checks like this:

```javascript
function isString(value) {
  return toString.call(value) === "[object String]";
}

function isIndexed(data) {
  return Array.isArray(data) || isString(data);
}
```

As for the `nth` function, the only thing we have in there that should be updated is the `_.isNumber` function from underscore. We can adapt our `isString` function to be an `isNumber` function with one tweak:

```javascript
function isNumber(value) {
  return toString.call(value) === "[object Number]";
}
```

So we can update the example to look like this:

```javascript
function isNumber(value) {
  return toString.call(value) === "[object Number]";
}

function isString(value) {
  return toString.call(value) === "[object String]";
}

function isIndexed(data) {
  return Array.isArray(data) || isString(data);
}

function nth(a, index) {
  if (!isNumber(index)) fail("Expected a number as the index");
  if (!isIndexed(a)) fail("Not supported on non-indexed type");
  if (index < 0 || index > a.length - 1) fail("Index value is out of bounds");

  return a[index];
}

function second(a) {
  return nth(a, 1);
}

second("abc"); // b
nth([1, 2, 3], 0); // 1
nth({}, 2); // Error: Not supported on non-indexed type
```

Now, let's add types. Our `is` functions are simple enough. Each one takes an unknown value and determines if it is something, so let's type them like this:

```typescript
function isNumber(value: unknown) {
  return toString.call(value) === "[object Number]";
}

function isString(value: unknown) {
  return toString.call(value) === "[object String]";
}

function isIndexed(data: unknown) {
  return Array.isArray(data) || isString(data);
}
```

Now let's add the types to our `nth` function. In the previous post, I typed the function as unknown, partially because I wanted to keep the spirit of how the function was written but partially so we could learn more about assertion functions. In a real TypeScript code base, we would write the types much more explicitly.

Our `nth` function takes two arguments: `a` and `index`. `index` is simple enough to type because it needs to be of type `number`. `a` could either be a string or an array. So first, let's create a type called `Indexed`:

```typescript
type Indexed = Array<unknown> | string;
```

This essentially says that `Indexed` could be an array of unknown values or a string. Now we could do something like this:

```typescript
function nth(a: Indexed, index: number) {}
```

But this will force the value of `a` to be an array of unknown values if we pass in an array. Let TypeScript infer what type of array we are passing in would be much nicer. Luckily we can do that using generics. Let's look at the updated types:

```typescript
function nth<T extends Indexed>(a: T, index: number): T[number] {}
```

Let's break that down. First, we are saying that `nth` takes a generic value of `T` that must extend the `Indexed` type we declared. Then we say that `a` is of type `T` and that we are returning `T[number]` or the value at the index of `T`.

By having `T` extend `Indexed`, we are now letting Typescript infer the array's values to know the return type accurately. For example, if we call `nth([1,2,3], 0)`, Typescript will know that the return type is of a type number.

The `second` function below can be typed the same way, except it wont need an explicit return type since it can infer that from the `nth` function we are calling. So now let's update the entire example using TypesScript:

```typescript
function isNumber(value: unknown) {
  return toString.call(value) === "[object Number]";
}

function isString(value: unknown) {
  return toString.call(value) === "[object String]";
}

function isIndexed(data: unknown) {
  return Array.isArray(data) || isString(data);
}

type Indexed = Array<unknown> | string;

function nth<T extends Indexed>(a: T, index: number): T[number] {
  if (!isNumber(index)) fail("Expected a number as the index");
  if (!isIndexed(a)) fail("Not supported on non-indexed type");
  if (index < 0 || index > a.length - 1) fail("Index value is out of bounds");

  return a[index];
}

function second<T extends Indexed>(a: T) {
  return nth(a, 1);
}

second("abc"); // b
nth([1, 2, 3], 0); // 1
nth({}, 2); // TypeError
```

There you have it, a fully modernized and typed example from the classic "Functional JavaScript." I highly recommend checking it out, and I'll see you next time.