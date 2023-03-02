---
title: "My Variable Naming Scheme in JavaScript"
datePublished: Thu Aug 12 2021 20:55:15 GMT+0000 (Coordinated Universal Time)
cuid: ckwzixzt30d2rb6s1011w827i
slug: my-variable-naming-scheme-in-javascript-c0a3f1f2dfb8
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316574825/utMA6o-ma.jpeg
tags: javascript

---

Photo by [Jon Tyson](https://unsplash.com/@jontyson?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Naming things in programming is very difficult. In a previous post, [I talked about my opinions when writing JavaScript.](https://non-traditional.dev/opinionated-javascript-4585b4b353e6) I recently read about [what not to do when naming variables](https://levelup.gitconnected.com/javascript-best-practices-variable-names-to-avoid-18f7cb47b5bb) by [John Au-Yeung](https://medium.com/u/5253c50d76c1) and I very much agreed with many of the points.

It made me realize that my previous post lacked some essential naming patterns that I like to adopt. Yes, naming things can be hard, but it can make it much easier to name things in our code if you have a naming scheme that you follow. So I thought I would share my personal rules here in this post.

### Functions and Method Names Should be Verbs

Functions and methods are actions that are being performed. So it goes to reason that they should be names that describe that action. For example:

```javascript
function checkOnlineStatus() {}

function getUserDetails(userId) {}

function removeDuplicates(array) {}
```

Naming functions this way clearly communicate what each of the functions does and even that they are functions in the first place. As things get passed around, it can get easy to lose track of what things are. Using an action verb helps communicate that this is something that needs to be called.

The one exception to this rule is old school JavaScript constructors, which were just functions called using the `new` operator, and React Components. In both cases, the functions are actually being used to create Objects, which leads me to my next rule.

### Objects and Primitives Should be Nouns

Variables that are assigned objects or primitive values represent something that has substance. It makes sense that we should use nouns to name these items of substance. For example:

```javascript
const userAge = 12;

const admin = {
  name: "Doe, John",
};

const hostCity = "Salt Lake City, Ut";
```

It also makes it clear when you return values from functions that they should be named nouns that correspond with the function:

```javascript
const onlineStatus = checkOnlineStatus();

const currentUser = getUser(userId);
```

### Arrays Should be Plural Nouns

Arrays are technically Objects in JavaScript, but they are unique in that they represent a list of things. So it makes sense that arrays should be a pluralized form of that noun.

```javascript
const friends = [
  /*...*/
];

const names = [
  /*...*/
];

const indexes = [
  /*...*/
];
```

It also makes the parameter used in the callbacks of array methods easier to name as well. Simply use the singular form:

```javascript
const friendNames = friends.map((friend) => friend.name);
```

### Objects Used as Key/Value Pairs Should Use The Suffix `Map`

JavaScript objects are pretty versatile. Besides representing things like users, they are also often used to map pairs of keys and values. In those cases, the variable name should also make sure that it is clear:

```javascript
const spacingMap = {
  xs: "0.125rem",
  sm: "0.25rem",
  md: "0.5rem",
  lg: "1rem",
  xl: "2rem",
  xxl: "4rem",
};
```

Now it’s clear that each key will be mapped to a spacing value that I will need.

### Nullable Values Should be Prefixed With `Maybe`

If a value could be undefined or null, you want to communicate that in your variable name. This makes it clear that when using that variable, that you need to write defensive guards that handle what happens when the variable is nullish:

```javascript
const maybeHostCity = findHostCityByState("ND");
```

It is clear that we are expecting a host city, but it might be null, so we can be sure to guard for that case in our code.

Setting up your own naming scheme will make it much easier to name things in the future, and naming things correctly can make your code easier to read and maintain.
