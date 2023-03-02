---
title: "Searching Algorithms for JavaScript Developers"
datePublished: Tue Sep 24 2019 13:31:02 GMT+0000 (Coordinated Universal Time)
cuid: ckwxavcwr0cqnuws1g1wj9qo8
slug: searching-algorithms-for-javascript-developers-c5beb6f5e859
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316719346/V5UgJ5ZwA.jpeg
tags: algorithms, javascript

---

Photo by [Evgeni Tcherkasski](https://unsplash.com/@evgenit?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Coming from a non-traditional background, one of the skills I felt most notably missing at first was computer science skills. People around me are throwing around words like time efficiency and “Big O” and I’m just nodding my little imposter syndrome head like I know what they are talking about.

Then there are algorithms, common patterns of solving problems that computer science majors just take for granted, while those of us from non-traditional backgrounds are just recreating the wheel solving problems that have been solved several times over.

I thought I would pull the rip the veil of jargon from your eyes and go over some common searching algorithms using JavaScript and how to describe their efficiencies.

### What the $&@\* is Big O Notation

I say this a lot, but Big O deserves a post on its own. But to put it simply, Big O is a common way to describe how an algorithm space and or time requirements grow and the size of the input grows. There are two important things to know about Big O:

1. Big O is about giving an estimate of the worst-case scenario. An algorithm in a particular scenario could be as quick as one step, but be 1000 steps on the next input. What Big O is trying to succinctly describe is that given an input o `n` how many steps will it take in the worst-case scenario to finish the algorithm.
2. Big O notation is not trying to be precise. Big O notation is about giving a general description of how much time the algorithm increases given input size. If an algorithm’s real steps are (n/2)+5 (totally just made that up just now) the Big O notation will pair that down to just n because it is trying to communicate that it is a linear change as input increases.

### The Scenario We Are Trying To Solve

The problem we are going to try to solve is finding the index of a certain item in an array. We are going to make some assumptions as we start working through these searching algorithms. The first is that the array is sorted from smallest to biggest and secondly, there are no duplicate values in the array.

We are going to be writing functions that will take an array and a value to be found and return the index of that item in the array. If the value is not there, then it will return -1. Now I know what you are going to say, there already exists an indexOf method on array objects that does exactly that. We will go over that at the end, but for now, just pretend it doesn’t exist.

### Linear Search

Linear Search is the most straightforward and easiest to wrap your head around. You start at the beginning of the array and keep iterating through until you find the value that matches and return the index at that location, otherwise return -1. This can be written like this:

```javascript
function linearSearch(arr = [], val) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === val) return i;
  }

  return -1;
}
```

Since the worst-case scenario is that it is not in the list, the only way this algorithm will know if it doesn’t exist is to get through all the items in the array. This means that the time complexity is expressed as Big O of n, or O(n), where n is the length of the array.

Given that we are assuming the array is sorted, you could easily tweak the algorithm to have it check every other one. This would cut the problem in half, but it would still be expressed as O(n), This is because the important factor is the length of the array itself. In other words, the growth in the time it takes to run the algorithm is directly correlated to the number of items in the array.

### Binary Search

The above example didn’t take advantage of the fact that the array was sorted. You find your answer by systematically dividing the problem in half each step until you find the answer. This is different than the example above where you jump every two. That divides the problem in half once, but binary search divides the problem in half at each step;

Let’s take a real-world example. If we had a physical dictionary and you were looking for the definition of a word. Typically you wouldn’t start from the beginning and go page by page. Instead, more likely what you would do is plop the book open to the middle. If you happened to get to the right page, that’s great, if not your problem is still half as large since you now know you only need to look in either the left half or the right half. You can continue to half the book until you find your answer.

We can do this same process with our arrays. We start with the beginning index and last index and find the midpoint of the array with this simple formula: `let midpoint = (beginning + end)/2;` Since the midpoint needs to be an integer, you will need to round, which one can simply do with the `parseInt` function.

Just like the dictionary example above, we now have 3 choices. If the value at the midpoint is correct, then we return that index. Otherwise, if the value we are looking for is less than the value at the midpoint than we change the end to be the midpoint -1. If the value is greater than the midpoint then you change the beginning to be the midpoint +1. Then you rinse and repeat until the beginning value is greater than the ending value, which would indicate that the value is not in the array.

Here is one way to write a binary search algorithm in JavaScript:

```javascript
function binarySearch(arr = [], val) {
  let beg = 0;
  let end = arr.length - 1;

  while (beg <= end) {
    let mid = parseInt((beg + end) / 2);

    if (arr[mid] === val) return mid;

    if (arr[mid] > val) {
      end = mid - 1;
    }

    if (arr[mid] < val) {
      beg = mid + 1;
    }
  }

  return -1;
}
```

So what is the Big O of this algorithm? Once again, the worst-case scenario is if the item is not in that array. This is calculated as how many times it takes to half the problem until you find the answer. Simplified down to it’s most relevant parts, binary search is calculated as O(log n). If you have ever looked at a log graph, it starts steep but flattens out as the number gets higher. [Here is an example taken from Wikipedia](https://upload.wikimedia.org/wikipedia/commons/c/c6/Internet_host_count_1988-2012_log_scale.png).

In short, using the binary search algorithm, even as the number of inputs grows the steps to complete the algorithm do not. The difference between 10 inputs and 100 inputs is the same as the difference between 100 and 1000 inputs.

There is another search similar to binary search called interpolation search. The details of interpolation search are beyond the scope of this post, but using our dictionary example above, one doesn’t typically go straight to the middle. We often use our knowledge of the alphabet to make a guess based on where it falls in that order. If the word starts with a “T” for example, we will probably choose a spot near the back of the book.

Interpolation search attempts to do this is a systematic way. The important thing to remember is that interpolation search requires that the array not just be sorted, but also have an even distribution of values. If it is evenly distributed, the algorithm calculates to O(log log n), but if it’s not evenly distributed, it calculates to O(n). If you are interested in the details of how to code it, [there is a really good blog on the subject](https://medium.com/@smellycode/demystifying-interpolation-formula-for-interpolation-search-211780c43269)

### What About indexOf Method

The indexOf method is a built-in method that can be called on array and will return the index of a searched value or -1 if it doesn’t exist. So a very obvious question is what is the big O of indexOf. The indexOf method is linear or O(n). The reason for this is that, unlike binary search, the indexOf method does not assume the array is sorted. When working with an unsorted array, linear search is the best way to search.

One might ask, why should I ever use indexOf is binary search has a more efficient algorithm. First of all, binary search requires a sorted array. The cost of sorting an array is no small thing. Also, the indexOf is implemented by the browser’s JavaScript engine which is written in a lower-level language, such as C++. These lower-level implementations are much more likely to run faster than JavaScript functions that do the same thing. This means, depending on the size of the array, indexOf can perform faster than a binary search algorithm, especially if one has to sort the array first.

This is just a small taste of searching algorithms, but understanding how these searches work can help you understand how your code works and if need be, optimize it so it can perform more efficiently.
