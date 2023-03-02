---
title: "What’s The Difference Between JavaScript And ECMAScript"
datePublished: Thu Feb 14 2019 20:33:41 GMT+0000 (Coordinated Universal Time)
cuid: ckwxsjn9y03fbahs1c4wx5q86
slug: whats-the-difference-between-javascript-and-ecmascript-e1ce929a7a65
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316716088/FioWrQdwa.jpeg
tags: javascript, ecmascript

---

Photo by [Max Chen](https://unsplash.com/@maxchen2k?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

In 2016, I decided to make the transition from a career in finance to a career as a web developer. One of the first technologies I started to learn was JavaScript. What made this interesting was that ES2015(also known as ES6) had just been released the year prior and people were still wrapping their heads around all the changes that had been added to the language. While learning, I kept hearing about this thing called ES6 and the importance of using it. I was confused! What is ES6? What did it have to do with JavaScript?If these were new features written to be added to JavaScript(JS), then why was is there an “E” and not a “J”. Wouldn’t JS6 make more sense?

Googling it did not help. I found out the ES6 stood for ECMAScript(typically pronounced ek-ma script) version 6 (which is now officially called ECMAscript 2015 due to the commitment to release updates to the language annually). What is ECMAScript? I thought I was writing in JavaScript? How are they related? What’s the difference between them?

After much more researching I finally learned what the difference between them is. I don’t want to get too much into the history of ECMAScript and why it exists. If you are interested in it, Wikipedia has a [very good article](https://en.wikipedia.org/wiki/ECMAScript) on it. What I found out was that ECMAScript is a language specification and JavaScript is the implementation of that specification.

To make this easier to understand, let's look at keyboards. There are many types of keyboards out there, but the most common one that most people know of is the QWERTY keyboard, so named because of the keys being laid out with QWERTY as the first 6 letters on the top row. QWERTY is a specification or a set of rules and tests that define how keyboards should be laid out. It doesn’t mean you can’t add extra features or capabilities to the keyboard. It just means that to call yourself a QWERTY keyboard, you implement all the features that the specification requires.

Many brands make QWERTY keyboards. If you were to compare an Apple Magic Keyboard with a Logitech keyboard built for a Microsoft PC, you will see that, despite all the differences, they have many similarities. This sameness is due to them implementing the QWERTY specification and allows them all to say they are QWERTY keyboards, even though they are very different from each other.

In that same vein, ECMAScript defines the rules and tests of how to Implement a language and JavaScript is the implementation of that specification. Unlike keyboards, there are not many other languages that implement the specification other than JavaScript. The two biggest languages that do are JScript and ActionScript. Because of this, it is very common for ECMAScript and JavaScript to be used interchangeably.

### How Are New Features Added to ECMAScript?

Languages like C# and Swift, though they are open source, are controlled by a single entity (Microsoft and Apple respectively). ECMAScript, on the other hand, is not controlled by any one single entity, rather it is controlled by a board, called TC39. This board is made up of representatives who have a vested interest in the future of ECMAScript.

The time between ES5 and ES6 was so great and had such a large amount of features, that the board also created a new process with the release of ES6 to allow features to move up at different speeds, based on the need for discussion and testing so that features that need more discussion don’t hold back other features that do.

There is a 5 stage process, starting with stage 0 to stage 4. Stage 0 is basically suggestions to add to the language. As you can imagine most things die at this stage. If someone from the board feels that a suggestion has merit, they will “champion” or “co-champion” the suggestion into a formal proposal, which is stage 1. From here, the board will discuss it and how it will impact not just the future, but the existing code already out in the interwebs. As you can imagine, many things “die in committee” at this stage.

If the board feels the proposal is worth adopting, it will be moved on to stage 2, the draft stage. This is a first draft of how the feature will actually look. Typically a babel transform will also be created at this stage so it can be tested in real-world scenarios. The API is considered unstable and adopting the usage is done at your own risk. It is assumed that anything that makes it to stage 2, will eventually be adopted.

When it is considered stable, it is moved to stage 3: the candidate stage. This is when the specs are finalized and final comments are made. Though changes can still happen, breaking changes to the API are not likely to happen at this stage. When everything is finalized and everything is considered finished, it will be moved to stage 4, the finished stage. This stage means it is done and is only waiting for the next annual release of ECMAScript. For a more detailed look into the staging process, check out the [official document of the process](https://tc39.github.io/process-document/) found at the GitHub of the TC39.

With this new stage process, JavaScript has become a much more robust language. Browsers are also much quicker to adopt these new features since it is much more transparent what will be added and what the requirements are. Because ECMAScript exists, the JavaScript we write is improving consistently and constantly.