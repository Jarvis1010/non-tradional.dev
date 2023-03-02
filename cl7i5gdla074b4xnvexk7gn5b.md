---
title: "The Power of Maybe in TypeScript"
datePublished: Wed Aug 31 2022 21:47:55 GMT+0000 (Coordinated Universal Time)
cuid: cl7i5gdla074b4xnvexk7gn5b
slug: the-power-of-maybe-in-typescript
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/RSsqjpezn6o/upload/v1661976812012/XZhFt078B.jpeg
tags: javascript, web-development, functional-programming, effective-typescripte

---

In a perfect world, every function and every API we work with would have one clear type that always gets returned. Unfortunately, we don't live in a perfect world. In the real world, the functions and APIs we use might not have something to return to use. We might get nothing back. For this reason, I have created a few utilities in TypeScript that I bring in pretty much any project I work on. These utilities all build on a powerful concept called the `Maybe`.

What is a `Maybe`? I was first introduced to a [`Maybe`](https://guide.elm-lang.org/error_handling/maybe.html) when exploring the [Elm programming language](https://elm-lang.org). The docs at Elm describe a `Maybe` best. This is what their docs say: "This is a type with two variants. You either have `Nothing` or you have `Just` a value."

That sounds simple enough, right? It either is something, or it is nothing. Well, that is where the JavaScript world makes things even more complicated. JavaScript doesn't just have a single `Nothing` type like many languages. Instead, it has two ways to represent `Nothing`: `null` and `undefined`. To take advantage of the concept of a `Maybe`, we need to simplify our `Nothing` states as either `null` or `undefined`.

First, we need to decide between the two. Because of the complicated history of `null` and the `typeof` bug that was introduced in the early drafts of JavaScript, I recommend that you embrace `undefined` as your `Nothing` state of choice. (If you want to read more about the bug, [there is a nice article by Sourav Debnath about the typeof null bug](https://dev.to/_ravo_lution/why-typeof-null-is-object-181))

That's well and good for the code you write, but what about other people's code? Many browser APIs return `null` for their `Nothing` state and other 3rd party libraries. What do you do about them?

What we need to do is convert the output from these APIs into a `Maybe`. I've created a simple, yet powerful utility that does just that:

```javascript
function convertToMaybe(value) {
  return value ?? undefined;
}
```

This one-line function is made very simple using the [nullish coalescing operator](https://non-traditional.dev/80-cleaner-javascript-code-using-optional-chaining-and-nullish-coalescing-3fb1d1df3867). Let's break it down. First, if the value passed in is neither `null` nor `undefined`, it will return the value passed in. If the value is `null` or `undefined`, however, it will return the value on the right side of the `??`, which in our case is `undefined`. This guarantees that no matter what value we pass in, it will never return a `null`.

That sounds great in JavaScript, but I rarely write in plain JavaScript anymore. Most of my projects use TypeScript. Luckily there isn't much more we need to do to make this work well in TypeScript.

First, let's create our TypeScript Utility type:

```typescript
type Maybe<T> = NonNullable<T> | undefined;
```

Here we are creating a Generic Type called `T`. If you are unfamiliar with Generics, Generics act as a placeholder for a future type not yet decided. Generics let us pass in a type, much like how we pass arguments to a function. We are then using the Generic `T` to pass into the `NonNullable` type, which basically ensures that `T` can't be `null` or `undefined`. We are then using the union type operator, `|`, to also allow `undefined` as an allowed value.

Simply put, The `Maybe` of any type will either be that type or `undefined` but never null. A `Maybe<number>`, for example, will be either `number` or `undefined`. The `Maybe<null>`, however, can only be `undefined` because `null` is not allowed.

Now that we have defined our `Maybe` utility type, let's update our function:

```typescript
function convertToMaybe<T extends unknown>(value: T): Maybe<T> {
  return value ?? undefined;
}
```

In the above function, we added another Generic. We are not just defining a generic type of `T`, but we are also requiring that it extend the `unknown` type. And then we are saying our `value` parameter must be of type `T` and that the return value from the function will be a `Maybe<T>`.

I know you are probably asking, "Why are we going through all that extra boilerplate?" This way, TypeScript can infer the types based on the type of the value passed into the arguments. Now we can be sure that any value we are given is a `Maybe` value. If you have TypeScript, it will make you write defensive coding to ensure you don't have an empty value. Even if you are using JavaScript, the function will help you remember that the value I am getting back from the function might be `undefined` and I need to write defensive coding to protect myself.

Using `Maybe` values in your code is very powerful. Your code will be stronger and more resilient to empty values and allow you to fail gracefully in a state you can control.
