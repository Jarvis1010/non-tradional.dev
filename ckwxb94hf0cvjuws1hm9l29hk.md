---
title: "Dealing with Promises in JavaScript"
datePublished: Fri Aug 30 2019 14:59:46 GMT+0000 (Coordinated Universal Time)
cuid: ckwxb94hf0cvjuws1hm9l29hk
slug: dealing-with-promises-in-javascript-99d94105a972
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316683499/zEUFpolRt.jpeg
tags: javascript, web-development, promises

---

As the humorous comic above illustrates, dealing with asynchronous actions can be hard. Waiting for the “eventual consistency” can be confusing and hard to deal with. To make matters worse, for the longest time, Javascript didn’t have a standard way to handle asynchronous functions and various communities built their own “standards” for dealing with them.

Luckily for all of us, Promises were added to the Javascript Specification in EcmaScript 6 (for more information on the difference between JavaScript and EcmaScript you can check out [my post](https://medium.com/the-non-traditional-developer/whats-the-difference-between-javascript-and-ecmascript-e1ce929a7a65) on the subject). Promises are Javascript global objects that have benefited the community in a couple of very important ways.

First, It standardized the way we deal with asynchronous functions. This means we can learn one standard and no matter the environment or library we are using, our knowledge and understanding of Promises will apply universally. The second benefit is that Promises have provided guarantees that were not standard prior. This lets the developer write less defensive coding.

### How Do You Use Promises

Once again it’s important to emphasize that Promises represent asynchronous action. It’s called a promise because it “promises” to resolve or reject (fail) eventually. Promises provide an API that allows you to pass callbacks that will handle those two situations.

Let's say we have a function called `getCustomerInfo` that will pull in our customer information asynchronously. Luckily for us, it was written using promises. This means that when we call the function, it will return a Promise object. We can now use this Promise object to start working on that data when it returns by using the built-in `.then()` method, like this:

```javascript
const promise = getCustomerInfo();

promise.then((customerData) => doSomething(customerData));
```

As you can see the `.then` method allows you to pass a callback that will be called with the data is resolved. It is guaranteed that the call back will not be called until the promise is resolved.

The cool thing about these callbacks is that not only can you do something with that data when it is returned, it also lets you return data from the function. Whatever you return from your function will be wrapped in another promise. This means you can do something like this:

```javascript
const promise = getCustomerInfo();

const promise2 = promise.then(doSomething);

promise2.then(doAnotherThing);
```

This is really cool, but admittedly this is very verbose. Imagine if we had three functions that needed to be run on the data. Then we have to keep track of 3 promises. Luckily, there is an easier syntax: method chaining. Using method chaining we can rewrite the above example like this:

```javascript
getCustomerInfo().then(doSomething).then(doAnotherThing);
```

This syntax improves the readability of our code since we get no real benefit by saving each promise in its own variable. Using this method, we can chain any number of functions together, passing the value from one function to another.

The obvious next question is how do I handle when the promise fails, throws an error or is otherwise puts the promise in a rejected state. This is where the `.catch` method comes in handy. Just like the `.then` method, `.catch`takes in a callback that is guaranteed to be called only if the promise is in a rejected state.

There are two things you need to remember about the `.catch` method. First of all, the `.catch`method can be chained just like the `.then` method, but unlike the `.then` method, it will only be called as soon as the promise is ‘rejected.’ This means that even if there are other `.then` methods higher up in the chain, the moment it is in a rejected state, it will jump immediately to the next `.catch` method, ignoring all other `.then` methods in the chain before it. For example:

```javascript
getCustomerInfo()
  .then(doSomething) // if this function throws an error
  .then(doAnotherThing) // this function will be skipped
  .catch(handleError); // and this function will be called instead.
```

If none of the prior functions put the promise in the rejected state, the `.catch` method will be ignored and it will go on to the next `.then` method, if there is one.

The other thing that you should know is that the `.catch` method does not end the chain. Whatever is returned from the `.catch` method, will be passed into the next `.then` method unless the `.catch` method throws another error. For example:

```javascript
getCustomerInfo()
  .then(doSomething)
  .then(doAnotherThing)
  .catch(handleError) // by default the return of this function
  .then(keepDoingStuff) // will go to this function
  .catch(handleError); // unless it throws an error
```

In the above example, the keepDoingStuff will be called even if the function before it was in the `.catch` method. This allows us to continue the chain and put in as many `.catch` methods where ever you need to handle the promise being put in a rejected state.

Sometimes, we need to clean up after our promise, no matter if the promise is resolved or rejected. These could include closing our connection to a database or removing a loading spinner on the web page. There used to be only one way to guarantee that the clean up was called. This was accomplished by passing in our cleanup function into both a `.then` and a `.catch` like so:

```javascript
getCustomerInfo()
  .then(doSomething)
  // ...
  .catch(handleError)
  .then(finalCleanup)
  .catch(finalCleanup);
```

The reason for this was that we needed to handle the cleanup no matter if the promise is in a resolved or rejected state, and the only way to ensure that we handled both scenarios was to use both method types. Luckily, another method type has since been added to the Promise object called `.finally` that addresses this concern.

This method type is unique in two ways. First, it always has to be the last method in the chain. Second, no value will be passed into the .finally callback function. This is because there is no way to guarantee which method would be called immediately before the `.finally`method. Basically, the cleanup function cannot know what state the promise is in and is only for actions that need to happen no matter what. If you do have a use case where cleanup is dependant upon the state of the promise, then you must use `.then` and `.catch` to handle those cases individually

Here is how we would change the above example:

```javascript
getCustomerInfo()
  .then(doSomething)
  // ...
  .catch(handleError)
  .finally(finalCleanup);
```

### Helper Functions

The Promise object also has a few static methods that are designed to help you work with Promises. Promise.resolve and Promise.reject are two static methods that let you quickly convert any value into an instantly resolved or rejected promise, like so:

```javascript
const resolvedPromise = Promise.resolve({ errors: false });
const rejectedPromise = Promise.reject({ errors: true });
```

This is very good when working on mock data that you know will eventually be consumed using promises.

The other most common helper function is Promise.all. Promise.all helps you manage more than one promise at a time. Promise.all takes an array of promises and will return a special promise. This promise will be “resolved” only when all the promises in the array are all resolved. Then it will pass in the array of values into the first `.then` method attached. If any single promise in the array rejects, however, it will immediately put the promise into a reject status which means, it will jump straight to the closest `.catch` method.

There is also a Promise.race method that is similar to Promise.all except instead of waiting for all the promises to resolve, it will resolve with the value of the first promise to resolve. There is also a proposal to add another static method called Promise.allSettled. [You can read more about that here](https://javascript.info/promise-api#promise-allsettled) if you would like.

Asynchronous functions are not easy, but with the help of promises, we now have a standard way to deal with them. This will ultimately lead to code that is easier to read.
