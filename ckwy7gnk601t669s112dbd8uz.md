---
title: "Async/Await: The Evolution of JavaScript Promises"
datePublished: Mon Sep 09 2019 13:31:01 GMT+0000 (Coordinated Universal Time)
cuid: ckwy7gnk601t669s112dbd8uz
slug: async-await-the-evolution-of-javascript-promises-6400692d38a9
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316701242/i4xUVlK6w.jpeg
tags: javascript, asynchronous, promises, concurrency

---

Photo by [WanderLabs](https://unsplash.com/@wanderlabs?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[In my previous post, I wrote about promises and how to use them.](https://medium.com/the-non-traditional-developer/dealing-with-promises-in-javascript-99d94105a972) Promises added a much-needed feature to JavaScript that was missing for many years: A standard. Now that we have promises, we have a standard way of dealing with anything asynchronous.

The Syntax, however, can be a bit foreign feeling. Passing in chains of callback functions can get cumbersome to deal with. What we want is to be able to write code that looks like this:

```javascript
try {
  const userData = getUserData(userId);
  //  ...handle resolved userData
} catch (e) {
  //  ...handle error
}
```

Luckily now we can. In 2017, we were introduced to a new JavaScript syntax: Async/Await. This new syntax gives us some new flexibility when working with Promises.

### How To Use Async/Await

The keywords are, async/await, are often mentioned together as if they are the same keyword, but they have two unique jobs. First, the `async` keyword is used to declare a keyword as an async function. Here are the various ways it can be used:

```javascript
async function getResults() {}

const getResults = async function () {};

const getResults = async () => {};
```

An `async` function is different from other functions in two important ways. First, it allows the function to use the `await` keyword (more on that later). Secondly, is that it will implicitly return a Promise. This means that something like this is possible:

```javascript
const getUserWorkList = async () => {};

getUserWorkList().then(doSomethingWithData);
```

As you can see, this syntax doesn’t lock you into one pattern or the other. As you use this syntax more, you will probably switch back and forth from Promise chains and Async/Await, depending on which syntax makes the most sense.

Now that we have declared our function to be `async` we can start using the `await` keyword. You use the `await` keyword anytime you want the function to pause while it waits for a Promise to resolve. Using our example at the beginning, we would write it like this:

```javascript
async function getUserData() {
  const userData = await getUserData(userId);
}
```

Once the function gets to the `await` keyword it will pause while the Promise to the right resolves (we will go over what happens when it rejects in a minute). Once it does, whatever data was resolved will be assigned to the `userData` variable. If you do not need to use the resolved data from a promise you can use it on its own, like this:

```javascript
async function sendEmails() {
  await sendWelcomeEmail();
}
```

In the above example, the await works the same way. It pauses the execution of the function while the Promise to the right resolves.

As we learned previously, Promises have two possible states: resolved and rejected. Using Promise chains, we used the `.catch()` method to catch when our Promise was put in a rejected state. The obvious question now is how do we do this in an async function. Luckily, it works just how we want it to, by using a `try/catch` block. We can take our example from the beginning and only need to make some minor changes to make it work.

```javascript
async function tryToGetUser() {
  try {
    const userData = await getUserData(userId);
    //  ...handle resolved userData
  } catch (e) {
    //  ...handle error
  }
}
```

As I mentioned earlier, promise chaining and Async/Await can be used together. One can await a promise chain and async functions can be chained themselves. Knowing how to use both promise chaining and Async/Await will help you be able to expertly handle promises as they come up in your apps.
