---
title: "More Things Developers Say and What They Mean"
datePublished: Thu Jan 31 2019 17:15:33 GMT+0000 (Coordinated Universal Time)
cuid: ckwzlo5wu0dbg69s14vuuhvw5
slug: more-things-developers-say-and-what-they-mean-f41f36c7f270
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316589281/mHhvFwOze.jpeg
tags: learn-coding, learning-journey

---

Photo by [NESA by Makers](https://unsplash.com/@nesabymakers?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Last Month, I published a post called [Things Developers Say and What They Mean](https://medium.com/the-non-traditional-developer/things-developers-say-and-what-they-mean-48956b6111c1). It was very fun and we had a great discussion on these things. Since that post, I have received even more bits of jargon that developers say and decided it was a good time to do a second post.

Just to recap the last post: there are a lot of bits of jargon and buzzwords that developers say that are not taught in courses and tutorials but are important to know. As a self-conscience developer, you tend to not want to stop and ask what these buzzwords are (even though you should) and so I decided to post these definitions in these posts so that you quietly and safely learn these definitions and your imposter syndrome can stay our little secret(#sarcasm).

**Currying:** The first time I heard this, I couldn’t help but think of my favorite Thai dishes. Given the context, I was pretty sure that currying a function did not have much to do with my favorite food, but still had no idea how curry fit into what people were talking about. I quickly learned that currying is a functional programming technique of separating parameters into multiple function calls. For example, we can take this:

```javascript
const add = (a, b) => a + b;

const result = add(1, 2);
// result === 3;
```

and turn it into this:

```javascript
const add = (a) => (b) => a + b;

const result = add(1)(2);
// result === 3;
```

This is also called a higher order function which also lets you create partially implemented functions like this:

```javascript
const addFive = add(5);
const addTwenty = add(20);

const result1 = addFive(3);
const result2 = addTwenty(2);

// result1 === 8;
// result2 === 22;
```

Why is it called currying? The technique is named after Haskell Curry, an important mathematician who has had a major influence in functional programming.

**Yak Shaving:** Have you ever started a task, only to realize that you must complete a separate task first. Upon attempting the new task you, once again realize that the task also has prerequisites. And this pattern continues on and on until you finally find the task that can be finished, opening up being able to work on all the other tasks until you are finally back working on the original goal that you set out to accomplish. This is Yak Shaving. Believe it or not, Yak Shaving gets its meaning from an episode of Ren & Stimpy about Yak Shaving Day. It would be too difficult to attempt to summarize the episode. You need to watch it in order to understand.

**Debounce:** As you can imagine, without context, hearing someone say that they need to debounce their function can be quite confusing for a new developer. How does a function “bounce” and then how do you “debounce it?”

Debouncing is a technique used to control how often a function is called. Take, for example, an auto-complete function for an input field. Without debouncing, each time the user clicks a letter on their keyboard a function is fired. This means that the average user would send about 3 function calls a second. That is too many calls. We are sending network requests and most of them will never be used. This will likely cause the user experience to experience slow down and jankiness for network calls that will never be used.

When we add debouncing, we limit the function’s ability to be called. In that same situation, the autocomplete function won't be able to be called until X seconds has passed. This significantly reduces the number of function calls, without hurting the experience of the user and will more likely improve the experience. Another technique with the same goal, but a different implementation is **Throttling.** Throttling limits the number of calls per second, without controlling the interval gap in between calls.

Why is it called debouncing? It actually comes from circuits. Think of a light switch, the reason it is held in place is some type of spring-like mechanism. As you flip the switch, it actually “bounces” which can result in multiple “calls” happening when we only wanted one. Think of a rubber ball bouncing, if you want to stop it from bouncing when you throw it, then you can flatten it and therefor debounce it.

**Memoize:** What is that. When I hear memoization I think of emails going out or literal memos being passed around the office. Memoization is actually a technique to speed up the calls of high-cost function calls. When we “memoize” a function, we cache the results of a high-cost function so that if the same parameters are used, we already have the information available and therefore we don’t have to keep paying the cost of running the function. It actually comes from “memorandum” which is Latin for being remembered.

There you go. I hope that helps so that you no longer have to feel like you are watching a foreign film without the subtitles. Please, if you can think of any other pieces of jargon that keeps you confused, send it my way. Then we can all be smarter together.
