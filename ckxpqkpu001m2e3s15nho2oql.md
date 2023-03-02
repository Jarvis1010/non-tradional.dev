---
title: "Things You Didn’t Know You Could Do With The Console in JavaScript"
datePublished: Fri Jun 21 2019 06:58:22 GMT+0000 (Coordinated Universal Time)
cuid: ckxpqkpu001m2e3s15nho2oql
slug: things-you-didnt-know-you-could-do-with-the-console-in-javascript-6eed68ccf534
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316596305/LzMD-a30B.jpeg
tags: javascript, console

---

Photo by [Anton Ponomarev](https://unsplash.com/@umadrummer?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

The first thing the many people learn when learning a new programming language is how to build a “Hello World” app. Which is pretty much what it sounds like, having your programming language print out “Hello World!” on to the screen (there is an interesting [article](https://blog.hackerrank.com/the-history-of-hello-world/) at hacker rank on the history of “Hello World” if you are interested). To do that in JavaScript, it only takes one line of code:

```javascript
console.log("Hello, World!");
```

Logging to the console isn’t just one of the first things you learn. It ends up being one of the most important debugging tools. One of the most common things to do when JavaScript isn’t running the way we think it should, is to add log statements at various point in our app so we can understand what is really happening in our app.

If all you know about `console` was that it could log things, you will probably be very successful, but:

![What if I told you you can do other things with console besides logging?](https://cdn.hashnode.com/res/hashnode/image/upload/v1638316593513/x5m06HKNu.jpeg)

Yes that’s right. The `console` object has more methods than just `log` and can help you bring your JavaScript debugging skills to a new level. Though each browser can have their own unique features, there are some common methods available on all browser. I thought I would share my favorite one’s here, but for a more complete list, check out [MDN](https://developer.mozilla.org/en-US/docs/Web/API/console)

### assert()

The things with `console.log` is that the only way to conditionally log something is to put it in an `if-block` and so this results in one of two things, we either don’t put it in an if block and always log and then visually filter through the log statements. Or write an arbitrary if statement in the middle of the code.

This is where `console.assert` shines. `console.assert` only logs if the first item passed in is false, otherwise it doesn’t log anything. Here is an example of it’s usage:

```javascript
console.assert(user.age > 20, user);
//Assertion failed: {name:"John Doe", age:15}
```

As you can see I am choosing to log the user object only if the user’s age is 20 or less, otherwise nothing is logged. Just like `console.log` you can pass as many arguments to it and it will log all of them. The only difference is that the first item passed will not be logged, it will only be evaluated as either true or false.

### table()

Using `console.log` is great when you have strings, integers, or other primitives. It starts to be less helpful when what you are trying to log is an object or an array or even an array of objects. Luckily, there is `console.table` to the rescue. Instead of simply logging the data, it will put the data in a table, which is typically easier to read. For example, if you had an array of user data, you could log it like this:

```javascript
console.table(userList);
```

And it’s out put would look something like this:

![User information being shown in tabular format](https://cdn.hashnode.com/res/hashnode/image/upload/v1638316594907/dengy2rGN.png)

As you can see, it will even infer column names based on the data given. If you need to, you can also pass in column names as an optional second parameter.

### time(), timeLog(), and timeEnd()

When you start having janky and slow performance, one of the most important things to do, is determine where your performance bottle necks are. A useful tool for that is controlled with three methods: `console.time` `console.timeLog` and `console.timeEnd.` These three functions allow you to start a timer with `console.time,` log it’s current value at any point in our code using `console.timeLog,` and stop the timer and logging it’s final state using `console.timeEnd.`

The browser will let you have up to 10,000 timers running at once. How do we keep track of them? `console.time` takes a string as a “label” in order to start a timer. You use that same label on `console.timeLog` and `console.timeEnd` to ensure you are working with the same timer.

### count() and countReset()

Sometimes all you need is to keep a running total, but to do that with just `console.log` would necessitate keeping track of the count in a global variable which is obviously a less than ideal solution. Luckily, we have `console.count` to do this for us. Each time you call `console.count` it will give the running total. The count will reset on either a page refresh or by calling `console.countReset.`

You can also keep track multiple different counts, simply by passing in a string as a label much like the `console.time` function. To reset an individual label just pass in that same string to the `console.countReset` function. This will reset only the label provided, and the others will continue on until they are reset manually or the page is refreshed.

This is just a part of the functions available on the console object, but they are the ones I find most useful for me on a day to day basis. Of course, checkout MDN to learn about the others and maybe you will find some of the others more useful for you.
