---
title: "Using JavaScript Proxies to Make Sorting Arrays Safe"
datePublished: Fri Feb 04 2022 19:52:35 GMT+0000 (Coordinated Universal Time)
cuid: ckz8tqv050kj1bbs1e2038ijq
slug: using-javascript-proxies-to-make-sorting-arrays-safe
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/yFlC156ZTCY/upload/v1643957466981/EQ7sYSRN7.jpeg
tags: proxy, javascript, sorting, array-methods

---

Array methods can be grouped into two categories: mutating and non-mutating methods. Methods such as `push`, `pop`, and `splice` are mutating because they change the array they are called on, for example:

```javascript
const myArr = [1, 2, 3];

myArr.push(4);
// myArr = [1, 2, 3, 4]

const popped = myArr.pop();
/**
 * popped = 4
 * myArr = [1, 2, 3]
 */

const spliced = myArr.splice(1, 1);
/**
 * spliced = 2
 * myArr = [1, 3]
 */
```

On the other hand, methods like `map`, `filter`, and `slice` are non-mutating because they do not change the array they are called on. Instead, they create a new array from them, like this:

```javascript
const myArr = [1, 2, 3];

const myArrPlusOne = myArr.map((x) => x + 1);
// myArrPlusOne = [2, 3, 4]

const onlyOdd = myArr.filter((x) => x % 2 === 1);
// onlyOdd = [1, 3]

const sliced = myArr.slice(1, 1);
// sliced = [2]

console.log(myArr);
// [1, 2, 3]
```

However, there is one method that is often mistaken as a non-mutating method but is mutating. This method is the `sort` method. The sort method takes a comparator function and sorts the array called on. What makes it "feel" like a non-mutating array is that it also returns the now sorted array:

```javascript
const unsortedArr = [5, 1, 2, 4, 3];

const sortedArr = unsortedArr.sort((a, b) => a - b);

console.log(unsortedArr);
// [1, 2, 3, 4, 5]

console.log(Object.is(sortedArr, unsortedArr));
// true
```

By returning itself, it allows us to chain the `sort` method among other array methods. Unfortunately, this means that we can accidentally mutate an array we never intended to:

```javascript
const clientIds = [5, 1, 2, 4, 3];

const sortedClients = clientIds
  .sort((a, b) => a - b) //oops just mutated the clientIds array
  .map(idToClientObject);
```

## "Fixing" the sort method

Wouldn't it be great if we could "fix" the sort method so that it would return a sorted array without sorting the array it was called on? Well, we can, by using `Proxy` objects. [`Proxy` objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy) let you pass in a target object and object of handlers that define the behavior of the proxy object. It will then return a new object that has this new behavior. Let's start by creating a function that will take an array and return a new proxy wrapped array:

```javascript
function createSafeSorter(arr) {
  return new Proxy(arr, {});
}
```

We can trap many behaviors, but the handler we want to define is the [`get` handler](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/get). The `get` handler is called whenever a property is retrieved from the target object. This includes when we call methods on the target. Knowing that, let's create our handler:

```javascript
function createSafeSorter(arr) {
  return new Proxy(arr, {
    get: (target, prop, receiver) => {
      if (prop === "sort") {
        return (comparator) => [...target].sort(comparator);
      }

      return Reflect.get(target, prop, receiver);
    },
  });
}
```

In the above code, we assign a function to the `get` property of our handler object. This function accepts a `target`, which is the object passed into the proxy, a `prop` which is the name of the property that the code is trying to get, and the `receiver`, which is either the proxy or an object that inherits from the proxy (not necessary for what we are doing).

The handler checks if the `prop` is the `sort` method. If it is, it will return a new function that takes a comparator and uses that function to sort a copy of the original array. Otherwise, we pass the `target`, `prop`, and `receiver` to the `get` method of the `Reflect` object. [The `Reflect` object is super simple](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect). It complements the `Proxy` objects in that it has static methods that you can create handlers for. Passing the arguments received in our handler function into the same method on the `Reflect` object will return the original value. That was a long way of saying everything else will work the same.

Now we can pass in an array and get back an array wrapped in a proxy that will use our safe sort method instead of the native sort method. Everything is excellent, right? We have one last thing we need to fix. We are copying the array and then sorting it in the above handler, but that new array is not wrapped in a proxy. This means that if we need to call sort again, we will end up mutating our new array, for example:

```javascript
const unsorted = createSafeSorter([5, 2, 4, 1, 3]);

// Works great
const lowToHigh = unsorted.sort((a, b) => a - b);

// Oops, we did it again
const highToLow = lowToHigh.sort((a, b) => b - a);
```

Luckily the solution is easy. We just need to do a little recursiveness and wrap our new sorted array in our `createSafeSorter` function:

```javascript
function createSafeSorter(arr) {
  return new Proxy(arr, {
    get: (target, prop, receiver) => {
      if (prop === "sort") {
        return (comparator) => createSafeSorter([...target].sort(comparator));
      }

      return Reflect.get(target, prop, receiver);
    },
  });
}
```

Now we can do this lot of code safely:

```javascript
const unsorted = createSafeSorter([5, 2, 4, 1, 3]);

// Works great
const lowToHigh = unsorted.sort((a, b) => a - b);

// Also safe!!!
const highToLow = lowToHigh.sort((a, b) => b - a);
```

## Why you shouldn't do this

Now that I have spent a whole blog post on how to make your `sort` method safe and non-mutating, I am going to recommend that you don't do this in your codebase ever. If you do this, you and your team may know that the array being passed around uses a safe sort, but not everyone else does. Other people and third-party packages have written code based on the fact that `sort` is mutating. If you change that behavior, you can create so many unintended consequences. It is this exact reason why browsers will never fix the `typeof null === 'object'` bug. So much defensive code has been written around this fact that you would break the web if you "fixed" it. If you like, create a `safeSort` utility for your project and use that any time you sort:

```javascript
function safeSort(arr, comparator) {
  return [...arr].sort(comparator);
}
```

Anyway, I hope you enjoyed this journey into using Proxy objects and why you shouldn't use them to "fix" the platform.
