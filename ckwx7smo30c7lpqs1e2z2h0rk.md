---
title: "Opinionated JavaScript"
datePublished: Wed Jun 30 2021 14:04:09 GMT+0000 (Coordinated Universal Time)
cuid: ckwx7smo30c7lpqs1e2z2h0rk
slug: opinionated-javascript-4585b4b353e6
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316763442/gVN2rtlKh.jpeg
tags: javascript, web-development

---

Photo by [Steve Johnson](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

As you write more and more code, you start realizing that there is more than one way to solve the problem. While everyone is looking for the “silver bullet” that will solve all the problems, there is no perfect way to code everything.

That said, it is healthy to form opinions on how to write code. Being “opinionated” is not a bad thing. In fact, it is very healthy to form opinions. That said, it is also just as important to understand why you have those opinions.

This post will highlight some of the stronger opinions I have developed for how I write JavaScript now and why I have those opinions.

### Avoid Truthy/Falsy Checks

A common check we look for in JavaScript is if we have a value or not. A clever trick that is often taken advantage of in JavaScript is that `null` and `undefined` are considered “falsy,” which lets us write code like this:

```javascript
if (!myVariable) {
  /* run code if variable is undefined */
}

/* or */

return myVariable ? myVariable : defaultValue;
```

As simple as this seems now, it has a few issues. First of all, the `0` and empty strings are also falsy values, which means the above code will run for situations other than just undefined or null.

Beyond that, the longer you separate yourself from when you wrote this code, the harder this code is to read. The above code isn’t clear if `myVariable` is a boolean or an object or what.

You can avoid both these problems if you explicitly check for the value you want:

```javascript
if (typeof myVariable === "undefined") {
  /* run code if variable is undefined */
}

/* or */

return myVariable ?? defaultValue;
```

The first example explicitly checks if the variable `undefined` and only runs the code if it is. The second example takes advantage of the [nullish coalescing](https://non-traditional.dev/80-cleaner-javascript-code-using-optional-chaining-and-nullish-coalescing-3fb1d1df3867) operator recently added to ECMAScript that lets you provide a fallback value only if the value being checked is `undefined` or `null.`

On a side note, if your variable potentially could be undefined, I highly recommend that you attach `maybe` to the front of the variable name to show that it might be undefined. For example, `maybeUser` would indicate that you have a user, but it could be `undefined`. Speaking of variables…

### Prefer `const` for Variable Declarations

As of this writing, there are three ways to declare a variable in JavaScript: `var,` `let,` and `const.` `let` and `const` were added to ES2015 and are block-scoped, which means they are not hoisted to the top of their lexical scope, like `var` is. For this reason, using `var` is generally frowned upon. I take it even one step further. I prefer to use `const` over `let.` [Though I am not the only one to think this](https://medium.com/pragmatic-programmers/signal-unchanging-values-with-const-8eef04629afb), it’s important to understand why.

The Javascript engine will enforce that variables declared using `const` cannot be reassigned. This gives you two things right out of the gateway. First, you can be confident that the value assigned to that variable won't be changed out from underneath you. You could have several lines of code after it is first declared and still be confident that the variable is still set to the same value.

The second is that it encourages clean, self-documenting code. When you run into a scenario where you would want to mutate a value, using `const` encourages you to assign it to another value, like this:

```javascript
const firstName = "Bob";

firstName = firstName + " Smith";
//TypeError: Assignment to constant variable.

const fullName = firstName + " Smith";
// "Bob Smith"
```

Just simply having the variable declared with `const` we were discouraged from mutating a value and instead assigned the full name a more accurate variable name for what the value represents.

### Don’t Return Booleans From Functions.

Booleans are one of the most simple types. It is either true or false. That’s it. It can be no other value. Despite that, they can be very confusing when they are the value returned from a function. For example, let us take these functions:

```javascript
checkUserAdminStatus(user);

getConnectionStatus();

checkUrlSafety(maybeDodgyUrl);
```

In the above examples, could you be certain what `true` represents? You could assume that `true` represents that the user is an admin, that the connection status is online, and that the URL being checked is safe. But it’s not absolutely clear that it is.

What do we do if we have to support three states instead of two? Now we have a potential cascading problem throughout our code. I don’t necessarily recommend writing code for problems we don’t currently have. In this case, what I recommend mitigates both problems.

Instead of returning a boolean, return one of two strings that represent the state. For example:

```javascript
checkUserAdminStatus(user);
// 'admin' | 'not-admin'

getConnectionStatus();
// 'online' | 'not-online'

checkUrlSafety(maybeDodgyUrl);
// 'safe' | 'not-safe'
```

We still get a boolean-like situation, but now we can do checks on actual human-readable strings representing that state we want to check. Adding another state to any of these functions is now much simpler and may even result in no cascading code changes since it still returns the original two states.

This is not an exhaustive list of all my opinions, but hopefully, this will help encourage you to think about your code and form your own opinions. If you like my opinions, go ahead and adopt them or make your own instead. No matter what, make sure you understand why.
