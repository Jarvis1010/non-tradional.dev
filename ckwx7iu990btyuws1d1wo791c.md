---
title: "Sorting Arrays in JavaScript"
datePublished: Thu Sep 26 2019 13:26:01 GMT+0000 (Coordinated Universal Time)
cuid: ckwx7iu990btyuws1d1wo791c
slug: sorting-arrays-in-javascript-4ec7ec30ff7a
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316767715/JYmeB7pCF.jpeg
tags: javascript, sorting, array

---

Photo by [Edu Grande](https://unsplash.com/@edgr?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Arrays are a very powerful tool for a programmer. They allow one to group related data under a single data type. There are many things one can do with an array, but sometimes it is important to have the array sorted in a particular order first. Luckily, Arrays in JavaScript provide a static method called that lets us sort an array very easily without spending a lot of time implementing a sorting algorithm ourselves.

Even though the sort method is not difficult, it is important to understand what is happening under the hood, even if at a high level. In this post, I plan on showing not just how to use the sort method, but a little bit about sorting algorithms in general and how the Array.sort method is implemented under the hood.

### How To Use The Array.sort Method

First of all, it is important to know that the `.sort` method sorts the array in place. This means two important things: it does not make copies of the array itself while sorting and the method permanently changes the underlying array or in other words, it mutates it. It is important to know this because if you use the sort method, it will leave the order of items permanently changed even if you pass the array into a function and sort it there.

By default, Array.sort requires nothing to be passed in to sort the array. Simply call the method and it will sort the array. The sort order is determined by temporarily converting each item to a string (if needed) and sorting them alphabetically in ascending order. For example:

```javascript
["Zoo", "Apple", "Jam"].sort();
//["Apple", "Jam", "Zoo"]
```

There are some obvious problems with this. First of all, What if we need to be descending? What about if we don’t want to sort alphabetically, such as the months of the year? How do we sort an array of objects?

Besides all these very obvious questions the biggest gotcha is that numbers converted to a string will not sort the way we want. Take this example:

```javascript
[3, 20, 100].sort();
//[100, 20, 3]
```

Luckily sort allows you to pass a compare function. The compare function must take two arguments, we will call this element 1 and element 2, and it will expect a numerical value. If the function returns a 0, it will be understood the two elements are equal. If it returns any value less than 0, then element 1 will be sorted lower than element 2. If it returns any value greater than 0, element 2 will be sorted lower than element 1.

Here is a simple number comparison function that will sort the numbers in ascending order:

```javascript
const numCompare = (a, b) => a - b;

[3, 1, 6, 10].sort(numCompare);
//[1,3,6,10]
```

In the above example, if `a` and `b` are equal it will return 0, which communicates that they are equal. If `a` is less than `b` than `a-b` will return a value less than 0, communicating that `a` should be sorted lower than `b` and the opposite is true if the `a` is greater than `b.`

If one needed the numbers in descending order, it is very simple to achieve this by switching the `b` and the `a` like this:

```javascript
const numCompare = (a, b) => b - a;

[3, 1, 6, 10].sort(numCompare);
//[10,6,3,1]
```

This works because the logic is flipped and it communicates that higher values should be sorted lower, giving us the descending order.

What about a custom sort logic such as the months of the year? The sort method doesn’t care what logic we write in our compare function, it only cares what is returned. Here is one way we could do that:

```javascript
const monthCompare = (a, b) => {
  const months = {
    January: 0,
    February: 1,
    March: 2,
    April: 3,
    May: 4,
    June: 5,
    July: 6,
    August: 7,
    September: 8,
    October: 9,
    November: 10,
    December: 11,
  };

  return months[a] - months[b];
};

["November", "January", "August"].sort(monthCompare);
//["January", "August", "November"]
```

In the function, we have created a mapping of the month name to a numerical value and then we can simply use our same number logic from out numCompare function

We can do similar things if the elements are objects. You have access to all the properties on each element and you can write custom logic to determine which object should be sorted lower.

### Sorting Algorithms

So far we have talked about writing comparing logic, but what about the logic that is used to do the actual sorting? [In the previous post, I talked about searching algorithms](https://medium.com/the-non-traditional-developer/searching-algorithms-for-javascript-developers-c5beb6f5e859) and how they are described using Big O notation, which is used to succinctly describe how a certain algorithm performs given an input. Just like searching algorithms, there are some common sorting algorithms as well. (This is not meant to be an exhaustive list of all sorting algorithms, rather a taste of the different types)

**Bubble Sort** is a pretty easy algorithm to understand. You got through each item in an array and compare it to the next item in the array. If those two items are out of order, you switch them and then you go on to the next item in the array. After going through each item, you do that same thing again until you can get through the array without making a switch. Here is a simple example:

It’s called Bubble sort because the larger items “bubble” up to the top. This algorithm is described as O(n²) or in other words, the algorithm grows exponentially as the size of the array increases

**Selection Sort** is another easy to understand algorithm. The algorithm systematically goes through the array and finds the smallest item in the array and puts it at the beginning. It then looks for the next lowest item and so on until the array is sorted. Selection sort is very similar to bubble sort except for the smaller items fall until the array is sorted. Just like bubble sort, Selection Sort is described as O(n²)

**Merge Sort** takes is similar to binary search and follows a divide and conquer pattern of sorting. Merge sort can be easily described in a few easy steps:

1. Divide the Array in Half
2. Sort the left half
3. Sort the right half
4. Merge the two halves.

Step two and three will then follow the same 4 steps recursively until you get an array of one. Merge sort, as opposed to the other algorithms, is described as O(n log n). This means, similar to Binary Search, the steps of the algorithm grow at a slower rate than the size of the array length.

### What Sorting Algorithm is Used by Array.sort

There is no requirement given by the ECMA script spec on how a JS Engine on any particular algorithm. I will specifically go over how the V8 engine implements it since both Chromium-based browsers (Google Chrome and future versions of Microsoft Edge) as well as Node use the V8 engine.

Up until recently, Array.sort used an algorithm called Quick Sort. Quick Sort is very similar to merge sort in that it is a divide and conquer algorithm. It’s a very efficient algorithm, but it had one problem: It wasn’t stable. To say that a sorting algorithm is stable means that if two items are considered equal they will still maintain the relative order from the original array. Unstable sorting algorithms provide no such guarantee. Here is an example of that can impact you:

```javascript
const people = [
  { name: "Bob", age: 87 },
  { name: "Jim", age: 87 },
  { name: "Sam", age: 32 },
];

people.sort(sortByAge);
```

Using Quicksort on the above example, `Sam` would be first, but either `Bob` or `Jim` could end up being next.

Recently, changed the sorting algorithm to use **Tim Sort,** which is a stable sorting algorithm while still being very efficient like Merge Sort or Quick Sort. [There is a great blog post from the V8 Developer team](https://v8.dev/blog/array-sort) that goes into this change in much more detail.

Sorting is a very important tool that a developer needs when working with arrays. Even though JavaScript has abstracted away from the actual implementation of how the array is sorted, it is important to understand what is being used under the hood so you can make better decisions as you code.
