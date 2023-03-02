---
title: "Dynamic Typing in TypeScript using Generics"
datePublished: Sat Jul 31 2021 14:17:27 GMT+0000 (Coordinated Universal Time)
cuid: ckwy7sfm101ub69s18v5x6teh
slug: dynamic-typing-in-typescript-using-generics-f88fa52428cb
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316696089/fdxjijioy.jpeg
tags: javascript, typescript

---

Photo by [Obi Onyeador](https://unsplash.com/@thenewmalcolm?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[In my previous post](https://non-traditional.dev/creating-a-timeout-wrapper-in-javascript-4e298a5fd42f), we created a generic function wrapper to set a timeout on the function. The function works amazingly well, but it is written in vanilla JavaScript. If you desire the extra confidence you get when writing in TypeScript, we need to take it a step further.

As a reminder, this was the final version:

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

In most cases, adding types is very easy. The `wait` function helper is simple to add a type. `milliseconds` is of type `number` and we can add that inline, like this:

```typescript
function wait(milliseconds: number) {
  return new Promise((resolve) => setTimeout(resolve, milliseconds));
}
```

The `opts` argument in the `withTimout` function can also be typed easily. It has two optional properties: `timeOut` which is type `number` and `errorMessage` which is type string. We could type that in line, but it would be much cleaner to read if we declared the type separately, like this:

```typescript
type Options = { timeOut?: number; errorMessage?: string };
```

Then we can use our new `Options`type inline in our function, like this:

```typescript
function withTimeout(promiseFn, opts?: Options) {
  //
}
```

Unfortunately our `promiseFn` argument is not so easy to type, and neither is the return type of our `withTimeout` function. This is because we are accepting any function as long as it returns a Promise. So how do we add types when we don’t know what type something is in the first place? That’s where Generics come in.

### TypeScript Generics

For a good primer on TypeScript Generics, I would recommend [Ross Bulat](https://medium.com/u/7cbf2b1d5a4e)’s [post on TypeScript Generics.](https://rossbulat.medium.com/typescript-generics-explained-15c6493b510f) To summarize, Generics allows us to put in placeholders for types, which lets those types be defined later on.

Let’s start with what we do know about the type of the promiseFn and the return type. We know they are both functions that return a Promise, so let’s add that in now:

```typescript
function withTimeout(promiseFn: () => Promise, opts?: Options): () => Promise {
  //
}
```

Both of our Promises need a type, which we don’t know in advance. This is a great time to add a Generic:

```typescript
function withTimeout<T>(
  promiseFn: () => Promise<T>,
  opts?: Options
): () => Promise<T> {
  //
}
```

In the above code, we set a placeholder `T` to stand in for whatever the type might be. And then, we are using it in our type definitions for our PromiseFn and return type. Then we can use it like this:

```typescript
const getUserWithTimeout = withTimeout<UserType>(getUser);
```

In the above code, we are setting the function's return type to be `Promise<UserType>` simply by defining the type between the two angle brackets when we call the function.

In a way, we can think of generics like arguments to a function that can be passed in. Just like arguments in a function, we can also set default types, like this:

```typescript
function withTimeout<T = void>(
  promiseFn: () => Promise<T>,
  opts?: Options
): () => Promise<T> {
  //
}
```

Now the return type will default to `void` if none is provided.

This is great, but we are still missing the types for the arguments of the function. We can define more than one generic like this:

```typescript
function withTimeout<K, T = void>(
```

We don’t know how many values, if any, that the function will take. So how do we type it? Remember how we used the rest operator, `...` to bundle all the arguments into an array called `args?` That array is an array of unknown values, which can be typed as `Array<unknown>.` With that background, we can type our arguments like this:

```typescript
function withTimeout<K extends Array<unknown>, T = void>(
  promiseFn: (...args: K) => Promise<T>,
  opts?: Options
): (...args: K) => Promise<T> {
  //
}
```

In the above code, we are saying that `K` can be any type as long as the type can extend `Array<unknown>.` Since an array of any type can extend an `Array<unknown>,` this will enforce that `K` must be an array. Then we define args as being of type `K` in both the promiseFn type and the return type.

(I would like to point out that you could refactor the promiseFn and the return type into a single type. However, in this case, I feel that would be too much indirection, and the types are very legible as in their current state)

Now we can call our function like this:

```typescript
const getUserWithTimeout = withTimeout<\[UserId\],UserType>(getUser)
```

This means that `withTimeout` will take and return function with a single argument that is of type `userId` and will return a `Promise<UserType>.`

Now everything is working correctly, but there is one more awesome thing left. TypeScript is pretty good at inferring types. This means that we actually don’t have to pass any types in at all, and Typescript will be able to infer the types directly

Let’s recall the above function, this time without declaring the types first:

```typescript
const getUserWithTimeout = withTimeout(getUser);
```

Our code is still simple to use like Vanilla JS, but has all the type safety that TypesScript gives us.

### The Final Version

Here is the final version of the the `withTimeout` function with all the types:

Generics gives our Typescript a superpower. Allowing us to maintain a level of dynamism while still gives us that confidence of type safety. If you haven’t already, I would recommend you try it out and see what problems it solves for you.
