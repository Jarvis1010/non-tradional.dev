---
title: "How To Code The “Fizz Buzz” Challenge Using JavaScript Generators"
datePublished: Thu Feb 21 2019 14:38:29 GMT+0000 (Coordinated Universal Time)
cuid: ckxpq8nhd032n1ms1e7vz78qb
slug: how-to-code-the-fizz-buzz-challenge-using-javascript-generators-ed7f9f9c18af
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316617024/OTbSIuU3h.jpeg
tags: programming, javascript, generators

---

Photo by [American Public Power Association](https://unsplash.com/@publicpowerorg?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Every company has a different interview process, but one thing that is common among most employers is giving a coding challenge. Also known as code katas, these challenges are used to evaluate both your problem-solving skills as well as your knowledge of the language you are writing it in. There are many common challenges out there, but one of the most well-known code challenges is the “Fizz Buzz” code challenge.

There are many variations of the “Fizz Buzz” challenge, but they all typically go something like this: Write a function that will print all the numbers 1- 100 in the console. If a number is divisible by 3, then print “Fizz” instead of the number. If the number is divisible by 5, then print “Buzz” instead of the number. If the number is divisible by both numbers, then print “Fizz Buzz” instead of the number.

A fun thing to do is to take language features you are trying to learn and use them to solve challenges like the “Fuzz Buzz” challenge. Recently, I was trying to get a better grasp of generator functions added in ES6 and what one could use them for. I decided to take my own advice and I coded up a solution to the “Fizz Buzz” challenge and here is what I learned.

### To Understand Generators, One Must First Understand Iterators

An iterator is an object that implements the iteration protocols. There is a detailed explanation of what these protocols over at [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols), but to put it simply, an iterator object must have a `next` method that when called will provide an object with a done property and a value property. The `done`property will only be `true` if the final value of the iterator has been consumed. An iterator also should not be able to be reset. So that means that one can continue to call the `next` method as many times as you like, but it once it returns that it is done, it will continue to return that it is done no matter how many times you continue to call next.

In JavaScript, there are certain objects that are considered “iterable.” What this means is that the object has a method, found at the `[Symbol.iterator]` property. When this method is called, it will return an iterator object. This iterator object can then be used to iterate through the object’s data. Arrays, Strings, Maps, and Sets are iterable natively. Objects, on the other hand, are not. Iterator objects make many of the new ES6+ features possible. Iterator objects are used under the hood in `for...of`operations, Array.from(), array destructuring, and when we use rest and spread operators in arrays.

### How Are Iterators Related to Generator Functions

Generator functions are functions, that when called, return a special type of iterator object called a generator. What makes generators unique is that the underlying function is not executed all at once. The underlying function is able to be ‘paused’ and doesn’t continue running until the next method is called.

There are 3 important parts to make a generator function. The first is that you must use `function*`when declaring the generator function. It does not matter if there is a space between the word function and the star or not. All that matters is that there is a star after the word function(arrow functions cannot be generator functions). The second is that you must use the `yield` keyword to ‘pause’ the function. Whatever expression is to the right of the `yield` statement will be assigned to the `value` property of the returned object with the`done` property as false.

The next time the `next`method is called, the function picks up where it left off and runs until it hits another yield statement. Once there are no more `yield` keywords or it runs into a `return` keyword, then the function will return an object with done as true and value as undefined. The generator cannot be reset, so all subsequent calls to next will continue to return that it is done. If you want to iterate again, you will need to call the generator function again to get a new generator object.

Here is a very simple example of a generator function:

```javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const g = gen();

g.next(); // {value: 1, done: false}
g.next(); // {value: 2, done: false}
g.next(); // {value: 3, done: false}
g.next(); // {value: undefined, done: true}
```

In the code, I am creating a generator function called gen. I call gen and it returns a generator object that I save to the variable`g.` When the next method is called on `g`, the function runs until it hits the first yield. The yield pauses the function and an object is returned with 1 for the value and false for done. Then when next is called again, the function picks up where it left off and runs until it finds yield again, resulting in yielding 2 for a value and the function pausing once again. This goes on and on until no more yields are available.

The yield keyword even works in loops. This means we can rewrite the above example like this:

```javascript
function* gen() {
  for (let i = 1; i < 4; i++) {
    yield i;
  }
}

const g = gen();

g.next(); // {value: 1, done: false}
g.next(); // {value: 2, done: false}
g.next(); // {value: 3, done: false}
g.next(); // {value: undefined, done: true}
```

The function pauses and yields the value of `i` mid-loop. Then when next is called again, the loop picks up where it left off and then yields the next value of `i`.

Knowing this, we can start writing our FizzBuzz generator function. Here is one way we could do that:

```javascript
function* fizzBuzzGenerator() {
  for (let i = 1; i < 101; i++) {
    yield evaluateFizzBuzz(i);
  }
}
```

This function will yield the results of our evaluateFizzBuzz function on every call to next until the loop finishes. (I’m not going to give you all the answers to the challenge, you will have to figure out your own implementation of the evaluateFizzBuzz function).

Now that we have a generator function, how do we go about iterating through each value and logging that to the console? If you remember, I spoke earlier that many of the new features added to ES6+ that do iterations, use iterator objects under the hood. One way we can finish our implementation is to use the `for...of` loop pattern, like this:

```javascript
function* fizzBuzzGenerator(start = 1, end = 100) {
  for (let i = 1; i < 101; i++) {
    yield evaluateFizzBuzz(i);
  }
}

const fizzBuzz = fizzBuzzGenerator();

for (let item of fizzbuzz) {
  console.log(item);
}
```

In the example above, you can see that instead of hard-coding the values into the for loop, I moved them to arguments with default values. This minor update allows the generator function to be more customizable, while still fulfilling the requirements of the challenge by default. We then call the generator function which returns a generator object, which we assign to the variable `fizzBuzz.` If we wanted to customize it we would call the function like this:

```javascript
const fizzBuzz = fizzBuzzGenerator(20, 50);
```

From there, we use the `for...of` on the fizzBuzz generator object which calls next and logs the value of `item`each iteration of the loop until fizzBuzz is done. (Make sure you don’t call `for...in` which doesn’t use the iterator underhood and returns the index of the array on each iteration of the loop).

This works, but we can make it better. The example above is very imperative and we would prefer to write it more functionally. Luckily, other features use iterator objects under the hood, including the spread operator. This allows us to change our implementation to look like this:

```javascript
function* fizzBuzzGenerator(start = 1, end = 100) {
  for (let i = 1; i < 101; i++) {
    yield evaluateFizzBuzz(i);
  }
}

[...fizzBuzzGenerator()].forEach((item) => console.log(item));
```

First of all, we are cutting out the intermediate fizzBuzz variable, since we are not using the generator object ourselves we don’t receive any benefit to saving it off into a variable. The other thing we are doing is using the spread operator to spread the values generated from the generator object into an array. Now that it is in an array we can use the forEach method, which passes each value of the array into whatever function we pass into the forEach method. As you can see, our function takes the item and logs it out.

This is just scratching the surface on what generators can do. I highly recommend you research them more at [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Advanced_generators). Is this implementation a bit of an overkill? Probably. I don’t necessarily recommend that you use this implementation in a job interview. What this does do, is let us get a better understanding of how generator functions work and how we would go about implementing them so when we run into a real-world need, we can be ready to use them.
