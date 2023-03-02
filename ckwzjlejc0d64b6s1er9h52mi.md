---
title: "A Practical Guide to Recursion For JavaScript Developers"
datePublished: Fri Sep 27 2019 21:36:41 GMT+0000 (Coordinated Universal Time)
cuid: ckwzjlejc0d64b6s1er9h52mi
slug: a-practical-guide-to-recursion-for-javascript-developers-727f81a04509
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316665831/ZOJTS3vCW.jpeg
tags: javascript, recursion

---

Photo by [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

The more you code, the more you find there is not just one way to solve a problem. The real craft in coding is learning to write code that not just solves the problem but does it in a way that is clean, eloquent, and readable. A pattern that is both powerful and eloquent is recursion.

### What is Recursion

Recursion is a way to iterate over a problem by having a function call itself (Yes, I said call itself) repeatedly until the result is found. Thinking of a function as being able to call itself might sound mind-boggling but it’s a very powerful way to describe a problem.

A common way to introduce people to recursion is a factorial calculation. In case you don’t remember what a factorial is, a factorial of any positive real number is the multiplication of all real numbers from 1 to that number. The way that you were taught to represent factorial in grade school was to use the exclamation point. So the `!3` is `3*2*1` which is 6. The `!10` is `10*9*8*7*6*5*4*3*2*1` which is 3,628,800.

If I asked you to write a function that calculated the factorial of a given value, You would probably notice that the solution to the problem is to incrementally repeat the same step of multiplication over and over again or in other words, the solution is looping. Given that knowledge, you might end up writing something like this (For simplicity, I am going to assume that only non-negative integers will be passed into this function):

```javascript
function factorial(num) {
  let result = 1;

  for (let i = 1; i <= num; i++) {
    result *= i;
  }

  return result;
}

factorial(3); //6
```

This works, but it is not as eloquent as is could be and forces one to take a moment as you read the code to understand what is happening.

Let’s look at this problem another way.`!0` is 1 and `!1`is also 1. `!2` can also be written as `2*!1`which is the same as writing `2*1` because `!1 === 1` . Still with me? What about 3? `!3` can be rewritten as `3*!2` because `!2 === 2*!1` which is the same as `2*1.`

So to summarize that pattern `!n` is just `n*!(n-1)` where n is the number you are trying to calculate the factorial of. So how would we express that in code? Here is one way to write that:

```javascript
function factorial(num) {
  if (num === 1 || num === 0) return 1;

  return num * factorial(num - 1);
}

factorial(3); //6
```

Let’s break that code down. The first line in the function checks if the value is 1 or 0 and if it is it returns. This is what is commonly called as the “base case” or the case where you break away from the rest of the code and no longer call another function.

It is typical to write your base case first before you write the rest of your logic so you ensure that you don’t get stuck in an infinite loop. It is also customary to put the base case at the top of the function as I have written up there.

The rest just simply follows the logic we already described. Multiplying the number by the factorial of the number -1. Do you see how much cleaner that is? It not only removed a whole loop, but also the need to maintain the state of the result on each iteration of the loop.

### A Real-World Problem

It’s one thing to code calculate the factorial of a number, but who is trying using JavaScript to do that? What is a real-world problem that is solved with recursion?

Let’s take the objects. In a [previous post on variable declaration](https://medium.com/the-non-traditional-developer/let-const-and-var-a-beginners-guide-to-variable-declaration-in-javascript-3486d6a4dfd5), I explained that variables defined with `const` cannot be reassigned. It is important that I used the word “reassigned” and not “changed” because objects, functions, and arrays cannot be reassigned, but they can be changed. Take this for example:

```javascript
const obj = { name : "Bob" };
obj = { name = "Sam" } //this will throw an error
obj.name = "Sam" //no error
```

This is because the properties of the object can still be changed even if the variable can’t be reassigned to another value.

In JavaScript, Object has a static method called `freeze` that lets you “freeze” each of the properties of an object so that those properties can no longer be reassigned. Now we can do this:

```javascript
const obj = { name: "Bob" };
Object.freeze(obj);
obj.name = "Sam"; //This now throws an error
```

The gotcha with `Object.freeze` is that it only does a shallow freeze, which means it only freezes the properties of the object passed in. If one of the properties was assigned another object as it’s value, the properties in that object are not frozen, like this:

```javascript
const obj = {
  name: "Bob",
  job: { title: "Worker" },
};

Object.freeze(obj);

obj.job.title = "Manager"; //no error
```

What we need to do is write some type of “deep freeze” function. Luckily, nested objects are a perfect use case for using recursion.

Let’s think through the problem first. Given a value, we want to determine if it is an object. If it is, we want to iterate over its properties and freeze them if they are objects and then freeze the object itself. Otherwise, do nothing.

So the base case of our `deepFreeze` function is determining if the value passed into the function is an object and if it isn’t, do nothing.

```javascript
function deepFreeze(obj) {
  if (typeof obj !== "object") return;
}
```

Good, we now have our base case set. Step two is to iterate over the property values and freeze them if they are objects as well. This is made easy using the `Object.values` static method, which takes an object and returns an array of all the values.

Given that it is an array, we can then call the `forEach` method which takes a callback function that is called on each of the values. Can you guess what function we are going to pass in as the callback? Exactly, the deepFreeze function is the perfect function to pass into it, like this:

```javascript
function deepFreeze(obj) {
  if (typeof obj !== "object") return;

  Object.values(obj).forEach(deepFreeze);
}
```

Now all we need to do is freeze the object itself and we are done:

```javascript
function deepFreeze(obj) {
  if (typeof obj !== "object") return;

  Object.values(obj).forEach(deepFreeze);

  Object.freeze(obj);
}
```

Now we can do this and get the expected results:

```javascript
const obj = {
  name: "Bob",
  job: { title: "Worker" },
};

deepFreeze(obj);

obj.job.title = "Manager"; //This now throws an error
```

Recursion can be hard to wrap your mind around, especially at first. When done right, recursion is a powerful tool that lets you write clean and elegant code that lets you do some amazing things.
