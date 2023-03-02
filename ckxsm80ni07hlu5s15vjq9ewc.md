---
title: "The Basics of Looping in JavaScript"
datePublished: Tue Sep 17 2019 13:31:01 GMT+0000 (Coordinated Universal Time)
cuid: ckxsm80ni07hlu5s15vjq9ewc
slug: the-basics-of-looping-in-javascript-c75495805bbe
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316533084/aMQDtqsJH.jpeg
tags: javascript

---

Photo by [Tine Ivanič](https://unsplash.com/@tine999?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the first challenges you need to learn to overcome when learning to program is avoiding the infamous copy-paste errors. There is a famous acronym in programming, D.R.Y., which stands for don’t repeat yourself. Just like anything, this can be taken to an extreme, but it’s a solid principle that has stood the test of time for a reason.

There are many patterns to help you avoid repeating yourself and one of the most basic patterns built into all programming languages is looping. There are two different loops that any programmer needs to address when writing code: Loops where the number of iterations is known beforehand and loops where the number of iterations is not known beforehand. (Just to be clear, the number may not be known until the program is already running, but as long as it is known before the loop starts it fits in the first category, otherwise, it fits in the second category.)

JavaScript, interestingly enough, has two ways to do loops that address these two situations. These two loops are typically called “For loops” and “While loops” for obvious reasons that you will see soon. In this post, I thought I would go over the basics of how to write these loops and their use cases. (Before anyone complains, I do understand there are technically more than 2 ways to do loops. All the loops are variations of the For loop or While loop are used for similar use cases as the ones I will be going over here.)

### While Loop

The while loop is probably the most basic form of looping. A while loop is very much like an if statement, both syntactically and it’s use case. The basic form of an if statement is if some condition is true, run this code, like this:

```javascript
if (condition) {
   // this code runs only if
   // the above condition is true
}

While loops look almost that same:

while (condition) {
   // this code runs only if
   // the above condition is true
   // and it will continue to run
   // while it is true
}
```

The biggest difference between the two examples, besides using the word `while` instead of `if,` is that the while loop will keep checking the condition after the code is run and if it is true, it will run again.

Suppose you needed to parse some a string and find all the indexes of a certain substring. Since there isn’t a way to know how many instances of this substring, we can use the while loop to go through the string like this:

```javascript
let indexes = [];
let currentIndex = stringToParse.indexOf(string, 0);

while (currentIndex !== -1) {
  indexes.push(currentIndex);

  currentIndex = stringToParse.indexOf(string, currentIndex + 1);
}
```

The above example initializes the currentIndex and then checks if its value is not -1, which indicates that there is no instance of that substring and then pushes that index onto the indexes array. It then looks for the next index. If the index is still not -1, it will continue to repeat until it does.

The biggest challenge to be aware of with while loops is the potential to inadvertently create an infinite loop. If you do not properly create a situation that breaks out of the loop, then your code will be stuck in the loop and will probably crash.

There is a variation of the while loop called the do/while loop. This loop will run the code in the block first, then check the condition after, like this:

```javascript
do {
  // this code runs once, but unless
  // the below condition is true
  // and it will not continue to run
} while (condition);
```

Let’s say you need to get user input and then only proceed if the input is valid. One could write that logic out like this:

```javascript
let userInput;
let isValidInput = true;

do {
  userInput = getInput();
  isValidInput = verify(userInput);
  if (!isValidInput) sendMessage("Please enter Valid Input");
} while (!isValidInput);
```

We always know we want to get an input from the client and we won't know if we need to loop until we have the input from the client. This is a perfect example of when to use a do/while loop.

**For Loops**

The other situation where one would need a loop is when you have a finite amount of times run. To do that using a while loop you would code it like this:

```javascript
let i = 0;

while (i < 3) {
  // this code will run
  // only 3 times
  i++;
}
```

As you can see there are three parts that make it work, the initialization, the loop condition, and the incrementation. Here is that same example, but labeled to emphasized those 3 parts:

```javascript
let i = 0; // the initialization

while (i < 3) {
  // the condition
  //...
  i++; // the incrementation
}
```

This process is so routine that they built it into the language using a for loop. Like this:

```javascript
for (initialization; condition; incrementation) {
  //  ...
}
```

In the above example, it is important to remember that each area is separated by a semi-colon and not a comma. I have spent much time trying to figure out what my code is giving me an error, only to find out I have used a comma instead of a semi-colon.

So we can rewrite our while loop like this:

```javascript
for (let i = 0; i < 3; i++) {
  // this code will run
  // only 3 times
}
```

Let's say we need to get three inputs from a user and put them in an array. We could write it like this:

```javascript
let userInputArr = [];

for (let i = 0; i < 3; i++) {
  const input = getInput();

  userInputArr.push(input);
}
```

JavaScript has two variations of the for loop, namely the for/in loop and as of ES6 the for/of loop. For/in loops are for iterating over traditional javascript objects. For/in loops don’t have 3 sections like the for loop, instead what you do is declare a variable `in` and your object. For example:

```javascript
const myObj = { a: 1, b: 2, c: 3 };

for (let key in myObj) {
  // key will be 'a', then 'b', then 'c'
}
```

When using for/in loops with arrays, it is important to note that the “key” of an array is just the index. So when using a for/in loop with an array you will not get the value at each index, instead, you will get the index.

The for/of loop is a recent addition that is specifically intended to iterate over iterable objects, like arrays, strings, maps, and sets. Syntactically, it is almost identical to the for/in loop except you replace `in` with `of.` Functionally they are different. Instead of getting the key, like the for/in loop, you get the actual value at that location. For example:

```javascript
const greeting = "Hi!";

for (let char of greeting) {
  //char = "H" then "i" and then "!";
}
```

It is important that for arrays, as I said in my post on [array methods](https://medium.com/the-non-traditional-developer/map-filter-reduce-the-holy-trinity-of-array-methods-16ce3bdb69e2), for loops should NOT be your go-to solution for iterating over arrays. Instead one should look to use `map, filter, reduce, or forEach` instead. That said **IF** you have determined that you have a performance problem **AND** your array method is the cause of that problem, then converting your code to a for loop is one of the ways to improve that performance.

Looping is an important strategy for improving the maintainability of your code. Knowing which type of loop and how to use them is an important step as you learn to make amazing code in JavaScript.
