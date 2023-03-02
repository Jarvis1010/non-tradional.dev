---
title: "Modernizing Code Examples from Books: parseAge"
datePublished: Sat Oct 30 2021 07:41:00 GMT+0000 (Coordinated Universal Time)
cuid: ckwolkabx03282zs1ghfs4up2
slug: modernizing-code-examples-from-books-parseage-a6a4177d5b38
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316503635/liYFgKgg8.jpeg
tags: javascript, functional-programming

---

Photo by [John Moeses Bauan](https://unsplash.com/@johnmoeses?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[My last post on modernizing `splat` and `unsplat`](https://non-traditional.dev/modernizing-code-splat-and-unsplat-78aa9d0dd77f) were so much fun I thought I would do it again with the following code example in the book, Functional JavaScript, that I decided to do it again. This time we are going to go over the `parseAge` function example.

(Reminder, I still would highly encourage you to get the book, so I won't be going over content in the book, except to explain the example)

In the book, we are presented with this example:

```javascript
function fail(thing) {
  throw new Error(thing);
}

function warn(thing) {
  console.log(["WARNING:", thing].join(" "));
}

function note(thing) {
  console.log(["NOTE:", thing].join(" "));
}

function parseAge(age) {
  if (!_.isString(age)) fail("Expecting a string");
  var a;

  note("Attempting to parse an age");
  a = parseInt(age, 10);

  if (_.isNaN(a)) {
    warn(["Could not parse age:", age].join(" "));
    a = 0;
  }

  return a;
}
```

In the above code, we have a few utility functions: `fail,` `warn,` and `note.` The functions are pretty self-explanatory. The `fail` function throws an Error of whatever string is passed in and the `warn` and `note`logs a string prefixed with “WARNING:” or “NOTE:” respectively.

The `parseAge` function then uses those functions while it attempts to parse the age of a string passed in. If a value other than a string is passed in, it will fail, and if the string fails to parse a number it will warn that It couldn’t parse age and then return `0` otherwise, it returns the parsed age value.

The first low-hanging fruit we can update is that we are using `console.log` for everything. Instead, we can utilize `console.warn` and `console.info` to give a bit more meaning to our console logs:

```javascript
function warn(thing) {
  console.warn(["WARNING:", thing].join(" "));
}

function note(thing) {
  console.info(["NOTE:", thing].join(" "));
}
```

The other quick update is that we can update our custom string to use template literals instead of joining arrays:

```javascript
function warn(thing) {
  console.warn(`WARNING: ${thing}`);
}

function note(thing) {
  console.info(`NOTE: ${thing}`);
}

// from the parseAge function body
if (_.isNaN(a)) {
  warn(`Could not parse age: ${age}`);
  a = 0;
}
```

The next thing we have is a reliance on underscore.js utility functions. One of those is the `_.isNaN` function. This was important at the time of the book’s writing because the global `isNaN` function in JavaScript was unreliable and did not work as expected. Luckily, an alternative method was added in ES2015 to the `Number` object that does the same thing. So let’s update our code:

```javascript
if (Number.isNaN(a)) {
  warn(`Could not parse age: ${age}`);
  a = 0;
}
```

The utility is the `_.isString` function. This function works well, but sometimes it doesn’t make sense to bring in a whole utility library just for a small part. Plus this is a great opportunity to understand what `_.isString` is doing under the hood. If you look at the [code in github](https://github.com/jashkenas/underscore/blob/master/modules/isString.js) we can see that it is simply doing something like this:

```javascript
if (toString.call(value) !== "[object String]") {
  fail("Expecting a string");
}
```

I’ll leave it to you to look into how that works and why this way is used. But if we combine all these things we now have a more modern version:

```javascript
function fail(thing) {
  throw new Error(thing);
}

function warn(thing) {
  console.warn(`WARNING: ${thing}`);
}

function note(thing) {
  console.info(`NOTE: ${thing}`);
}

function assertIsString(value) {
  if (toString.call(value) !== "[object String]") fail("Expecting a string");
}

function parseAge(age) {
  assertIsString(age);

  note("Attempting to parse an age");
  const a = parseInt(age, 10);

  if (Number.isNaN(a)) {
    warn(`Could not parse age: ${age}`);
    return 0;
  }

  return a;
}
```

Besides what we have already gone over, there are two other changes in the above code. One is simply changing out `var` to `const` and pre-emptively returning `0` if the the parsed value is not a number.

The other change is more stylistic choice, but one I personally like. I refactored out the string check into it’s own function called `assertIsString.` This is primarily a stylistic choice, but it is also helpful when as we start converting it to TypesScript. Speaking of…

Most of the functions are simply typed:

```javascript
function fail(thing: string) {
  throw new Error(thing);
}

function warn(thing: string) {
  console.warn(`WARNING: ${thing}`);
}

function note(thing: string) {
  console.info(`NOTE: ${thing}`);
}

function assertIsString(value: unknown) {
  if (toString.call(value) !== "[object String]") {
    fail("Expecting a string");
  }
}
```

`fail,` `warn,` and `note` all take a string, so we added that type to the parameters of each function. `assertIsString` takes an unknown value and checks if it is a string. So it makes sense for it’s parameter to be of type `unknown.`

As for the `parseAge` function, it would be safe to assume we should type the parameter as a `string,` and honestly that would probably be the right choice in most apps. That said, the spirit of how the function is written implies that the value is unknown so let’s use the `unknown` type.

The problem is that, despite the fact that we are verifying the type of the age parameter is a string in our `assertIsString` function. TypeScript still will see that the value is unknown and complain when we try to call `parseInt` with an unknown value.

We could wrap the remaining code in a if statement that check the type of age, but honestly why are we doing another type check just to satisfy TypeScript (plus TypeScript will still complain on not writing code for all potential branches, so we didn’t really solve the problem).

What we need is to somehow let TypeScript know that once we get past the `assertIsString` function that the age parameter can safely be assumed to be a string at that point. Luckily, TypeScript has a way to do just that. Let’s make one small change to the type of our `assertIsString` function:

```javascript
function assertIsString(value:unknown):asserts value is string {
 if(toString.call(value) !== '[object String]'){
 fail("Expecting a string");
 }
}
```

In the above code, we added `:asserts value is string` where we would normally define our return type. What this does is instead of defining a return type from the function, we are telling TypeScript that what ever was passed into the function can be safely be used as a string.

We can do this, because if the value is not a string, we throw an error and the code doesn’t proceed on. If, however, the function runs without errors, we can be confident that it is in fact a string. It is important to note, that TypeScript is taking our word for it. TypeScript isn’t actually looking at our code to see if it works (it would be [impossible](https://en.wikipedia.org/wiki/Halting_problem) for it to do so). So you do need to be confident that it is implemented correctly.

Let’s look at the full TypeScript version:

```javascript
function fail(thing:string){
  throw new Error(thing);
}

function warn(thing:string){
  console.warn(`WARNING: ${thing}`);
}

function note(thing:string){
  console.info = console.log
  console.info(`NOTE: ${thing}`);
}

function assertIsString(value:unknown):asserts value is string{
  if(toString.call(value) !== '[object String]') fail("Expecting a string");
}

function parseAge(age:unknown){
  assertIsString(age)

  note("Attempting to parse an age");
  const a = parseInt(age, 10)

  if(Number.isNaN(a)){
    warn(`Could not parse age: ${age}`);
    return 0
  }

  return a
}
```

So there you have it. A fully modernized code example, complete with TypeScript. Let me know what you think and I look forward to sharing more examples with you soon.
