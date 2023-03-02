---
title: "Creating a Timeout Wrapper in JavaScript"
datePublished: Thu Jul 29 2021 14:03:13 GMT+0000 (Coordinated Universal Time)
cuid: ckwtm51750ebksms15zs2fago
slug: creating-a-timeout-wrapper-in-javascript-4e298a5fd42f
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316774405/c1VItqTTV.jpeg
tags: programming-blogs, javascript

---

Photo by [Nathan Dumlao](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Not long ago, I was reading [Ben Ilegbodu](https://www.benmvp.com/)’s blog, and he went over how to [extend the fetch API to allow the user to set a timeout.](https://www.benmvp.com/blog/quickie-fetch-timeout/) It’s a great read, and I highly recommend it. I thought it would be awesome to riff off that post and take it a couple more steps further.

First, I wanted to make it even more generic by wrapping any function that returns a Promise, not just the Fetch API. Second, I wanted to do it in TypeScript. In this post, I plan on the first goal, and then we will convert it to TypeScript in a future post.

### The Final Code

Before we go into the details, here is the final product:

```javascript
function wait(milliseconds) {
  return new Promise((resolve) => setTimeout(resolve, milliseconds));
}

export function withTimeout(promiseFn, opts) {
  const timeOut = opts?.timeOut ?? 2500;

  const errorMessage =
    opts?.errorMessage ?? `Your Request timed out after ${timeOut}ms`;

  return (...args) =>
    Promise.race([
      promiseFn(...args),
      wait(timeOut).then(() => {
        throw new Error(errorMessage);
      }),
    ]);
}
```

### The wait Helper Function

First, we have made a helper function called `wait.` [Ben goes into this helper function](https://gist.github.com/Jarvis1010/0f962833a126640b45078c36d0a46f0d) in detail, so I will summarize. The `wait` function takes a time in milliseconds to wait. It then returns a Promise that will resolve after the time in milliseconds has passed. We will go into why it is important in just a moment.

### The withTimout Function

Next, we define a function, `withTimeout,` that takes two arguments. First, it takes a function that returns a Promise. It doesn’t matter what the function does, just as long as it returns Promise. Second, it takes an options configuration object. This object is used to configure both the timeout length and the error message if it does.

In fact, that is what we are doing on the next two lines. We are [taking advantage of optional chaining and the nullish coalescing operators](https://non-traditional.dev/80-cleaner-javascript-code-using-optional-chaining-and-nullish-coalescing-3fb1d1df3867) to either use the timeout and error message provided in the options object or sensible defaults.

### Return Our Wrapped Function

The final part of the function returns a new function. This function is intended to take the same arguments as the promise function passed in so that we can pass those arguments to that function. Unfortunately, we don’t have a way of knowing what arguments, if any, that it takes.

Luckily we can take advantage of [the rest](https://medium.com/pragmatic-programmers/using-the-rest-parameter-1eb8a7ed0e8f) and [spread operators](https://medium.com/pragmatic-programmers/the-spread-operator-975c6ea37c35). We use the rest operator to wrap up all the arguments passed into an array and assign them to `args` from there, we can then spread all the values in `args` into the passed in promise function.

We are not simply calling the promise function on its own. What would the point of that be? Instead, we are calling inside of a Promise.race method. Promise.race takes an array of Promises. The first one to either resolve or reject is what will be returned from the method.

That is where our `wait` helper function comes in. We call it with our timeout value. If it resolves before our promise function, it will then throw an error. Otherwise, the promise function will resolve, and the `wait` function will be ignored.

There you have it. We have our function that takes any promised-based function and will return a new identical function that will throw an error if it times out. In the next post, we will go over how to use Generics to add types to your `withTimout` function.
