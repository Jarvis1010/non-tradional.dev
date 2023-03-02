---
title: "Modernizing JavaScript Code: The Comparator Function"
datePublished: Fri Dec 10 2021 16:40:42 GMT+0000 (Coordinated Universal Time)
cuid: ckx0m8ekv02pt5ns1a4hg6f5x
slug: modernizing-javascript-code-the-comparator-function
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/-80JPdRkDMA/upload/v1639093074870/uvqR5H_l3.jpeg
tags: javascript, web-development, functional-programming

---

After three posts on the subject, I decided to officially make this [a new series called Modernizing JavaScript Code.](https://non-traditional.dev/series/modern-js). This one will be short and sweet but still helpful as we continue down the path of learning about functional programming.

## Comparator Functions vs Predicate Functions

Before we go into the code, we need to go into some terminology. There are generally two ways to write functions that compare things. There is the intuitive way which is to take your inputs and return `true` or `false`, like this:

```javascript
function checkIsLessThan(x, y) {
  return x < y;
}
```

These functions are called **predicates**. Another comparing function will take two values and return a number greater than `0` if the first value is greater than the second, less than `0` if it is less than the second, and return `0` if they are equal. These functions are called **comparators**. It is common to see it used in the `Array.sort` method:

```javascript
[5, 1, 3, 8].sort((a, b) => a - b);
// [1, 3, 5, 8]
```

The difficulty is that comparators are not always intuitive to write, especially when what you are comparing are not numbers but instead are objects of users. They are also not generally useful outside of those contexts that need them.

In the book, Functional Javascript, we are given a function called `comparator` that takes a preditcate function and returns an comparator function that does the same logic. Here is that original code example:

```javascript
function comparator(pred) {
  return function (x, y) {
    if (pred(x, y)) return -1;
    else if (pred(y, x)) return 1;
    else return 0;
  };
}

[5, 1, 2, 8].sort(comparator((a, b) => a < b));
// [1, 2, 5, 8]

[5, "1", "2", 8].sort(comparator(isString));
// ["2", "1", 5, 8]
```

(Technically, in the book, each of the `pred` calls are wrapped in a function called `truthy`, but that function is not really part of this discussion, so I am intentionally leaving that out. I think you can guess what the `truthy` function does on a high level.)

The above code takes a predicate function, and it will return a function that takes two arguments. It will first pass the arguments to the predicate function in the order it received them. If it returns a `true`, the function will return `-1`. Otherwise, it will check the value in the predicate function again, but this time with arguments switched. Once again, if the value is `true`, the function will return `1`. If neither function calls return `true`, it will return `0`.

So let's modernize it. [In a previous post](https://non-traditional.dev/modernizing-code-splat-and-unsplat-78aa9d0dd77f), I mention that I have two rules of function styles:

1. I prefer the traditional syntax for top-level, named functions.
2. I prefer the arrow syntax for anonymous functions, with implicit returns if the return is simple

The other thing that I prefer, is [to avoid the `else` statement in my if-blocks.](https://non-traditional.dev/how-to-make-your-conditional-statements-easier-to-read-in-javascript-6e8c3f5e44f6) Given those two things, we can refactor our `comparator` function like this:

```javascript
function comparator(pred) {
  return (x, y) => {
    if (pred(x, y)) return -1;
    if (pred(y, x)) return 1;
    return 0;
  };
}

[5, 1, 2, 8].sort(comparator((a, b) => a < b));
// [1, 2, 5, 8]

[5, "1", "2", 8].sort(comparator(isString));
// ["2", "1", 5, 8]
```

Now let's add types. First of all, I think it is worth our time to define a `PredicateFunction` type and a `ComparatorFunction` type:

```typescript
type PredicateFunction = (x: unknown, y: unknown) => boolean;

type ComparatorFunction = (x: unknown, y: unknown) => 1 | -1 | 0;
```

In the above, we state that both functions take two unknown values and will either return a boolean if it is of a `PredicateFunction` type or `1 | -1 | 0` if it is of a `ComparatorFunction` type. One could argue that a `ComparatorFunction` type only needs to return a `number`, but I would prefer to be more specific if that is possible.

So now the only thing we need to do is to add that to our function signature, like this:

```typescript
function comparator(pred: PredicateFunction): ComparatorFunction {
  return (x, y) => {
    if (pred(x, y)) return -1;
    if (pred(y, x)) return 1;
    return 0;
  };
}

[5, 1, 2, 8].sort(comparator((a, b) => a < b));
// [1, 2, 5, 8]

[5, "1", "2", 8].sort(comparator(isString));
// ["2", "1", 5, 8]
```

Luckily, nothing else is needed. All other types can be inferred from these two types added to the function signature. We have a nice type-safe utility function that takes in a predicate function and returns a comparator function.
