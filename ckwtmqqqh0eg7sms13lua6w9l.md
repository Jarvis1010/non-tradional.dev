---
title: "Map, Filter, Reduce: The Holy Trinity of Array Methods"
datePublished: Fri May 10 2019 04:37:06 GMT+0000 (Coordinated Universal Time)
cuid: ckwtmqqqh0eg7sms13lua6w9l
slug: map-filter-reduce-the-holy-trinity-of-array-methods-16ce3bdb69e2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316754884/aHT6CA7xS.jpeg
tags: javascript, array, programming-tips

---

Photo by [Alexander Watts](https://unsplash.com/@alexanderwatts?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

I have learned a few programming languages over the years and there are many common things that most languages have. Two things that are almost universal in all of them are Arrays and for…loops. For…loops and Arrays go hand in hand in most languages, so much so that they are often taught together, or at least close together. It’s just how you typically iterate over an array.

So when I learned JavaScript and needed to iterate over an array, you can guess what tool I typically used. Sure I would use some of the variations of the For…loop, such as the For…in and then the For…of when I started learning ES6 syntax, but they were all based on the same pattern of a For…loop, just with syntactic sugar on it.

Then I started to learn React and I learned about this method built into arrays called `map` and my world was opened up. I learned that For…loops were not the only ways to iterate over an array. As I started to learn about `map` I found out that it wasn’t the only method available to iterate over an array. Around this time, a mentor of mine made the claim to me, “In Javascript, if you are using a For…loop with an array, then you are probably doing something wrong.”

I have since come to the same conclusion, the methods built into Javascript arrays are extremely useful. Since learning them, I can’t remember the last time I have actually used a for…loop in JavaScript, let alone to use one to iterate over an array. Of the various Array methods, the three most useful have got to be `map` `filter` and `reduce` and so I thought I would devote this post to how to use them.

(I think I would also mention that I have 3 runner ups as far as Array methods that I use most frequently. These are `forEach` `join` and `sort.` I am not going to devote any time in the post on how to use them, but you can learn about them at MDN [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach), [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join), [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort))

### Mutating Methods vs Non-Mutating Methods

Array methods can, for the most part, be grouped into one of two groups: Mutating and Non-Mutating methods. Mutating methods change the actual array that it is called on. These methods include `splice` `push/pop` and `shift/unshift.` `map` `filter` and `reduce,` on the other hand, are Non-Mutating methods. This means that the underlining array is left unchanged when the function is finished. This is important to understand as we go over how each of the arrays works.

### The Map Method

The `map` method creates a new array with the results of calling a provided function on every element, like this:

```javascript
const items = [1, 2, 3, 4];
const doubled = items.map((x) => x * 2);

console.log(doubled);
// [2,4,6,8]
```

It’s important to understand that this new array will ALWAYS be the same size as the original array, even if you don’t return anything. This method is used when you need to do some type of transform on every element. The map actually passes in a 2nd and 3rd argument, but they are used much less often, but you can find out more about them at [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

### The Filter Method

The `filter` method creates a new array with all items that pass the test implemented by the function that is passed into it. Unlike the `map` method, `filter` will return an array that is the same size or smaller(it will always return an array though, even if it is empty). Here is an example of how it is used:

```javascript
const items = [1, 2, 3, 4];
const justEven = items.filter((x) => x % 2 === 0);

console.log(justEven);
// [2,4]
```

There are two important things to pay attention to when using the`filter` method. First, is that it doesn’t change any of the underlying items, it simply adds them to the new array if it passes the test. Second, is that you need to be aware of JavaScript’s truthy/falsy gotchas. The function is expecting a boolean to be returned, but if you return something that is not a boolean, it will coerce it into a boolean. This is where understanding the nuances of [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) and [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) are important.

Just like `map` there is a 2nd and 3rd argument that is passed to the function, which you can learn more about at [MDN’s documentation on the filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) method.

### The Reduce Method

The `reduce` method is the most versatile method out of all of them. It applies a function against an accumulator and each element in the array to **reduce** it to a single value. The function passed takes in two arguments, the accumulator and current item, and what is returned is the accumulator for the next function call. Once it has been run on all of the items in the array, then the final accumulator value is returned. Here is a simple example of it in action:

```javascript
const items = [1, 2, 3, 4];
const sumOfItems = items.reduce((acc, cur) => acc + cur);

console.log(sumOfItems);
// 10
```

The `reduce` method, in addition to the function, takes an optional second value. If you pass in this value, it will be used as the initial value of the accumulator, otherwise, the value at the 0 index will be used as the initial value and function won't be called on that item. Even though it is optional, I highly advise you to not leave it out. Using the above example you can see how much something can change by changing the initial value:

```javascript
const items = [1, 2, 3, 4];
items.reduce((acc, cur) => acc + cur, 0); //10
items.reduce((acc, cur) => acc + cur, ""); //'1234'
```

Once again, there is an optional 3rd and 4th argument that you can learn more about at [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

### Method Chaining

The great thing about these methods is that you can chain them together. So if you need to filter and then reduce you can, simply by chaining them together like this:

```javascript
const bookObject = bookArray
  .filter((book) => !book.read)
  .map(({ title, author }) => ({ title, author }))
  .reduce((acc, cur) => {
    acc[cur.title] = cur;
    return acc;
  }, {});
```

In the above example, I am filtering down to the books I haven’t read, then taking out only the title and author for each book. Finally, in the reduce, I am turning the array into an object, using the title as a key to each book.

Arrays are such an important data structure in any language, and luckily JavaScript has some fantastic ways to make it easier to iterate over them. And remember, if you are using a for…loop on an array, you are probably doing it wrong!
