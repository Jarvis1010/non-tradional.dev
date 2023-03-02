---
title: "Understanding JavaScript’s Data Types"
datePublished: Thu Sep 12 2019 18:58:48 GMT+0000 (Coordinated Universal Time)
cuid: ckx272xxi0bjnzvs1hfib2rvs
slug: understanding-javascripts-data-types-fb57982d7a4a
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316641224/gJIt4yko5.jpeg
tags: javascript

---

Photo by [Marc Mueller](https://unsplash.com/@seven11nash?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Despite what some people will tell you, JavaScript does have types. All programming languages have built-in data types and JavaScript is no exception. Data types influence the memory usage of the data as well as constraining what operations can be performed on a single piece of data and even how those operations behave.

[In a previous post](https://medium.com/the-non-traditional-developer/let-const-and-var-a-beginners-guide-to-variable-declaration-in-javascript-3486d6a4dfd5), I talked mentioned how JavaScript is Dynamically typed, which means that variables are not associated with any data type and can be assigned and reassigned to any type. But what we didn’t talk about was what data types exist in JavaScript and how that can impact our code. So in this post, I felt it would be good to pull back the curtains and demystify JavaScript Data Types.

### Value Types vs Reference Types

There are various ways to group data types, but I find that the way that has the most practical benefit is to group them by how they are stored in variables. All data types are either stored as values or stored by reference.

Data types that are stored by value, also called primitive data types, can be thought of as storing the actual value in that variable. When that variable is passed to functions or copied to other variables the value is passed. Changes that happen in that function or on the copy don’t impact the original value.

For example:

```javascript
let num = 5;

function addTwo(num) {
  return num + 2;
}

function addTwo_v2(numBER) {
  return numBER + 2;
}

const newNum = addTwo(addTwo_v2(num));

console.log(newNum); // 9

console.log(num); // 5
```

Looking at the above example, even though the `num` variable was passed through two functions, the original value stayed unchanged. These values are immutable and cannot be changed. When performing operations on these values, the values are not changed. Instead, new values are given based on the operations performed on them.

Reference types, on the other hand, are not considered to store the actual value itself. Instead, what they store is a reference to that value. This means that when you pass the variable into functions or copy the values from one variable to another, you are not passing the value around, but a reference to that value.

For example:

```javascript
const user = { name: "Bob" };
const copyOfUser = user;

function changeName(person) {
  person.name = "Samuel";
  return person;
}

const newUser = changeName(copyOfUser);

console.log(user.name, copyOfUser.name, newUser.name);

// Samuel, Samuel, Samuel
```

As you can see, the changes made to one of the variables, changed all of them. This is because they all reference the same value and when a change is made, the change is made to the same referenced value.

The two data types also differ in how comparison operators work. Because value types are immutable, you can easily compare two values and determine if they are the same. The number 1 will never be anything other than 1 and the same goes for the other value types.

Reference types, because they only store references to the value and not the value itself, are not as straightforward when doing comparisons. When you are comparing reference types, what you are comparing is the reference itself. Thus means two values could be the same, but would still be considered different if they did not share the same reference.

This example summarizes this well:

```javascript
const num = 45;
const otherNum = 45;

console.log(num === otherNum); // true

const array = [1, 2, 3];
const otherArray = [1, 2, 3];

console.log(array === otherArray); // false
```

### Programmatically Getting The Type

Before going into the various data types, I think it is helpful to understand how to get the type of value in JavaScript. JavaScript provides a nice helper operator to get the type of any value. This helps write defensive programming so you don’t try to perform operations on values that don’t support them.

The operator is the `typeof` operator. It can be used with a space after it, like this `typeof x` or it can be used as a traditional function call `typeof(x).` The `typeof` operator will evaluate the value and return a string of the type, such as `string` or `object.`

### Value Types

JavaScript has 7 primitive types that are value types, two of which are recent additions. Most of these could justify a blog post of their own, but I will instead give a brief overview, but you can check out [MDN for a more in-depth article on each of these.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)

**Boolean** is probably the simplest data type. It represents one of two values: true or false. They are used in conditional statements and loops to decide how and if the code should proceed.

**Strings** are probably the next simplest data type. Strings represent textual data. They act like an array of characters, but strings are not arrays. They are an immutable data type of their own. Just like any other value type, operations do not change the actual string but provide a new string instead.

**Null** and **Undefined** are very similar in that both represent nothing. Undefined is the default value for undeclared variables and object properties. Null, on the other hand, represents the intentional absence of a value. The interesting bug that has and will continue to persist in JavaScript is that calling `typeof(null)` will return `"object"` and not `"null"` like we would expect. At this point, it will not be fixed because too much code is written assuming this bug is there.

**Number** is a unique data type. Most languages distinguish between Integers, floating-point numbers, double-precision floating-point, and others. JavaScript, on the other hand, only has one data type to represent all numbers, including integers and real numbers (JavaScript recently added a new number type called BigInt, more on that below). Besides the actual numerals, the number type has 3 symbolic types: +Infinity, -Infinity, and NaN which means not a number.

**Symbol** is a recent addition, added to ES2015. Symbols are guaranteed to be unique and immutable values that are generated at runtime. Once again, an entire blog post could be written ([and has been written](https://hacks.mozilla.org/2015/06/es6-in-depth-symbols/)) to go into why and how to use them. But to summarize, they provided a guaranteed unique value that could be used as a semi-private property on an object. There is a more to it than that and not likely something you would use on a day to day basis when building apps.

**BigInt** is the most recent addition to the primitive value types (as of this writing it is in Stage 3, which means it will be part of a future release of ECMA Script. That said most of the major browsers already support it or will in the next release). As JavaScript continues to mature and be used outside of just web browsers, the need to work with integers larger than the JavaScript number type can safely handle has increased. This is the motivation for adding BigInt. It allows JavaScript to handle this “Big Integers” that the number type could not safely and reliably handle.

### Reference Types

Technically there is only one data type in JavaScript that is a reference type: **Object.** Objects encompass many things that are probably obvious: Objects, Arrays, Maps, Sets and Functions (Even though Functions are objects, they are evaluated as `"function"` when evaluated by the `typeof` operator. What is less obvious is that all the primitive values except `null` and `undefined` have Wrapper Objects that wrapper primitive values. This is what makes one able to call methods, like `toUpperCase()` on a string primitive. These wrapper objects, String, Number, BigInt, Boolean, and Symbol, will be evaluated as `"object"` when past into the `typeof` function.

Understanding JavaScripts data types, even at just a high level, is important when writing clean and predictable code. Your apps will be more stable and easier to debug once you understand how JavaScripts data types work.
