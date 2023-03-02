---
title: "80% Cleaner JavaScript Code Using Optional Chaining and Nullish Coalescing"
datePublished: Thu Oct 15 2020 14:03:14 GMT+0000 (Coordinated Universal Time)
cuid: ckwmz50qc04eqygs1h2fiekr7
slug: 80-cleaner-javascript-code-using-optional-chaining-and-nullish-coalescing-3fb1d1df3867
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316494085/dkym6_3XJ.jpeg
tags: javascript, codenewbies, programming-tips

---

Photo by [Curology](https://unsplash.com/@curology?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Ever since ES2015, also known as ES6, JavaScript has moved away from a waterfall style approach where loads of features were released at once under a major version, to a more agile approach where new features are released annually as the spec is completed. This has allowed new language features to be added every year since. These features can be large and small, but they all tend to be well battle-tested through the use of transpiling and polyfills before their official release. In fact, when they actually become “official” it’s actually pretty underwhelming since the community has been using them for some time.

Two of those features moved to stage 4 nine months ago and were released in the ES2020 spec. Both of these features help developers work with Null and undefined values in a cleaner way and when used together, make some very concise, yet powerful code. These two features are Optional Chaining and Nullish Coalescing.

### Optional Chaining

Properties on JavaScript objects, like many languages, are often accessed by using dot notation. When you use dot notation one accesses the properties of the object by appending a dot and then the name of the property, like this:

```javascript
const book = {
  title: "My Favorite JS Functions",
};

book.title; // My Favorite JS Functions
```

This means that if one of the properties is another object, we can continue to “chain” property names simply by adding another dot, like this:

```javascript
const book = {
  title: "My Favorite JS Functions",
  author: {
    firstName: "Travis",
    lastName: "Waith-Mair",
  },
};

book.author.firstName; //Travis
```

This is all good, but what if our book is coming from a book API that doesn’t guarantee to have the info on the author and if it does, it still doesn’t guarantee that they have both the first and/or the last name of the author. This can make writing code as simple as this:`book.author.firstname` very unpredictable and dangerous.

In the past, we would have to write guards that checked for values. This is what our guards could look like:

```javascript
const authorName =
  book.author && book.author.firstName ? book.author.firstName : "Unknown";
```

This works, but this relies on checking for the `truthyness` of the values, which can backfire if the value is a valid value but is `falsy`, such as an empty string. A safer way to write the above would be:

```javascript
const authorName =
  book.author !== undefined && book.author.firstName !== undefined
    ? book.author.firstName
    : "Unknown";
```

Look at all we have to do just to assign one of two possible strings. Luckily with optional chaining, we can add a `?` before the dot and this will attempt to retrieve the value. If there is no value, then it will return `undefined.` This means we can rewrite the above like this:

```javascript
const authorName =
  book?.author?.firstName !== undefined ? book.author.firstName : "Unknown";
```

This is so much easier to read. It can be cleaned up though. To do that we need to learn about the other important feature: Nullish Coalescing.

### Nullish Coalescing

A common need when programming in JavaScript, or any language, is to have a safe fallback when a value ends up being empty or not existing. In JavaScript, there are two ways that empty or non-existence is defined. It could either be `undefined` or `null.` Since both `undefined` and `null` are considered `falsy` in JavaScript, one of the tricks for providing fallbacks was to use the `||` operator, which will return the first truthy value. It might look something like this:

```javascript
let maybeValue = "I exist";
let safeValue = maybeValue || "value";

console.log(safeValue); // I exist
```

When the above sentence is evaluated, `maybeValue` is ‘I exist” which is truthy. If `maybeValue` was a falsy value, then safeValue will fallback to “value”. The problem is that an empty string is a valid value, but is evaluated as falsy. So it will go to our fall back value even though we don’t want it to.

This is where the nullish coalescing operator comes in. All you need to do is replace the `||` with `??` and it works exactly as intended. What the nullish coalescing operator will do is evaluate the first value and only if it is “nullish”, or in other words `null` or `undefined,` will actually use the fallback. So we can rewrite the above like this:

```javascript
let maybeValue = "";
let safeValue = maybeValue ?? "value";

console.log(safeValue); // ""

maybeValue = null;

let safeValue = maybeValue ?? "value";

console.log(safeValue); // "value"
```

### Putting Them Together

Remember our long ternary from the top of the article. Using Optional chaining, we improved it a lot. With our Nullish Coalescing operator we can remove the entire need to use a ternary. Since the optional chaining will evaluate to `undefined` if any of the values are missing, we can pair that with our nullish coalescing operator to provide a nice simple fall back, like this:

```javascript
const authorName = book?.author?.firstName ?? "Unknown";
```

We have gone from several lines and several checks for undefined values to a single line of code that will provide the first name of an author if it is available, otherwise it will fall back to another acceptable value. This is much cleaner and easier to understand what is going on and therefore, much easier to maintain.

As JavaScript continues to improve, more features like the above two features will be released and will greatly improve the way we write JavaScript. This will make it that much easier to write clean easy to maintain code.
