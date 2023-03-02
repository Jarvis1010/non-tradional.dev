---
title: "Modernizing JavaScript Code: CSV/Table Manipulation"
datePublished: Fri Feb 25 2022 02:30:45 GMT+0000 (Coordinated Universal Time)
cuid: cl01srylc0c29l0nvexe17vuj
slug: modernizing-javascript-code-csvtable-manipulation
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/u3ajSXhZM_U/upload/v1645727812198/YYHLm-df5.jpeg
tags: javascript, typescript, functional-programming

---

In previous posts, we have worked through some of the code examples of the book Functional JavaScript. I'm going to go over the final example from Chapter 1 where the author shows how to parse a CSV file into a table, or more accurately an array of arrays of strings, that we can then use functions to manipulate into new tables with the data grouped and ordered differently.

## The Original Code

Let's start by looking at the raw code examples from the book:

```javascript
//  people.csv
//  Name,   Age, Hair
//  Merble, 35,  red
//  Bob,    64,  blonde

function lameCSV(str) {
  return _.reduce(
    str.split("\n"),
    function (table, row) {
      table.push(
        _.map(row.split(","), function (c) {
          return c.trim();
        })
      );
      return table;
    },
    []
  );
}

var peopleTable = lameCSV(peoplsCSV);
// [
//     ['Name','Age','Hair'],
//     ['Merble','35','red'],
//     ['Bob','64','blonde'],
// ]

function selectNames(arr) {
  return _.rest(_.map(arr, _.first));
}

function selectAges(arr) {
  return _.rest(_.map(arr, second));
}

var zipped = _.zip(selectNames(peopleTable), selectAges(peopleTable));

console.log(zipped);
// [["Merble","35"], ["Bob","64"]]
```

In the above code, we have a function called `lameCSV` that parses a CSV file into an array of string arrays. We then have two helper functions, `selectNames` and `selectAges` that both do similar things. They both map over the array to retrieve either the first or second item in each array ([we talked about the second function in a previous post](https://non-traditional.dev/modernizing-examples-the-nth-function)). After that, they both use the `_.rest` utility from underscore.js to remove the first item in the array and return the rest.

Finally, we use both of those select functions with the `_.zip` utility to merge the results back together.

## Modernizing

To modernize, let's start off with some low-hanging fruit. First, we can remove the `var` variable declarations and use either `let` or `const`. It is also good practice to use arrow functions as callbacks, so let's use those when appropriate. We no longer need the `_.map` and `_.reduce` functions so we can move those over to native array functions. In addition, I prefer to avoid mutating arrays, so let's change our `table.push` to `table.concat`.

```javascript
function lameCSV(str) {
  return str
    .split("\n")
    .reduce(
      (table, row) => table.concat(row.split(",").map((c) => c.trim())),
      []
    );
}

function selectNames(arr) {
  return _.rest(arr.map(first));
}

function selectAges(arr) {
  return _.rest(arr.map(second));
}
```

The next thing we can do to modernize is move away from using the `_.rest` function from underscore. In modern JavaScript we can use the rest parameter to do the exact same thing:

```javascript
function selectNames(arr) {
  const [, ...rest] = arr.map(first);
  return rest;
}

function selectAges(arr) {
  const [, ...rest] = arr.map(second);
  return rest;
}
```

While we are talking about removing underscore functions, lets make our own zip functions:

```javascript
function zip(...args) {
  const longestLength = Math.max(...args.map((arg) => arg.length));

  const arrayOfIndexes = Array.from(Array(longestLength).keys());

  return arrayOfIndexes.map((i) => args.map((array) => nth(array, i)));
}
```

In our `zip` function, first, we are collecting all the arrays passed in under a single array called `args` using the rest parameter. From there we use the `args` to find the length of the longest array in the args. After that, we create an array of indexes with this code: `Array.from(Array(longestLength).keys())`. What this does is create an array with the same length as the `longestLength`. By calling the `keys` method on that array we then get an iterable of all the indexes of that array. Since it is an iterable and not an array itself, we wrap the whole thing in `Array.from` to convert it back to an array.

Finally, the last thing we do is map over the `arrayOfIndexes` and use that index to create an array of all the values at that index in each array.

So here is the final version of the modernized code:

```javascript
//  people.csv
//  Name,   Age, Hair
//  Merble, 35,  red
//  Bob,    64,  blonde

function lameCSV(str) {
  return str
    .split("\n")
    .reduce(
      (table, row) => table.concat(row.split(",").map((c) => c.trim())),
      []
    );
}

var peopleTable = lameCSV(peoplsCSV);
// [
//     ['Name','Age','Hair'],
//     ['Merble','35','red'],
//     ['Bob','64','blonde'],
// ]

function selectNames(arr) {
  const [, ...rest] = arr.map(first);
  return rest;
}

function selectAges(arr) {
  const [, ...rest] = arr.map(second);
  return rest;
}

function zip(...args) {
  const longestLength = Math.max(...args.map((arg) => arg.length));

  const arrayOfIndexes = Array.from(Array(longestLength).keys());

  return arrayOfIndexes.map((i) => args.map((array) => nth(array, i)));
}

var zipped = zip(selectNames(peopleTable), selectAges(peopleTable));

console.log(zipped);
// [["Merble","35"], ["Bob","64"]]
```

## Now with TypeScript

First let's type our `lameCSV` function:

```typescript
type Table = string[][];

function lameCSV(str: string): Table {
  return str
    .split("\n")
    .reduce<Table>(
      (table, row) => table.concat(row.split(",").map((c) => c.trim())),
      []
    );
}
```

It takes a string, so that is easy to type. It technically returns an array of string arrays, which is `string[][]`, but often it is nice to give a type like that a name that has more meaning. In our case, we have created a type `Table` that is technically a `string[][]` under the hood. This not only gives the return value of this function more meaning, it gives our other functions meaning as well. We can type our select functions like this:

```typescript
function selectNames(arr: Table) {
  const [, ...rest] = arr.map(first);
  return rest;
}

function selectAges(arr: Table) {
  const [, ...rest] = arr.map(second);
  return rest;
}
```

This makes it more clear that these functions are intended to be paired with the `lameCSV` function since their input is the output of the `lameCSV` function is their input. Unlike some type systems, TypeScript is a structural type system. This means that you can still pass in anything that happens to have the same type as `string[][]` to these functions and it will work. That said, by giving it the type `Table` you are using your types to do a form of domain modeling, which simply put is documenting the relationship between the parts of your code.

The final thing we need to do is type our `zip` function. Let's start with the finished version and then let's explain it:

```typescript
function zip<T extends Array<unknown[]>>(...args: T): T[number][] {
  const longestLength = Math.max(...args.map((arg) => arg.length));

  const arrayOfIndexes = Array.from(Array(longestLength).keys());

  return arrayOfIndexes.map((i) => args.map((array) => nth(array, i)));
}
```

In the above code, we are declaring that the `args` are of type `T` and that `T extends Array<unknown[]>`. This allows TypeScript to do two things. First, it will enforce that all the arguments passed in are arrays. The other thing that it will do is infer the type from the arrays passed. This brings us to the return type. We are declaring that the return value is `T[number][]`, which means that the return will be an array of whatever type is at any index of `T`. So now our final typescript version looks like this:

```javascript
//  people.csv
//  Name,   Age, Hair
//  Merble, 35,  red
//  Bob,    64,  blonde

type Table = string[][];

function lameCSV(str: string): Table {
  return str
    .split("\n")
    .reduce<Table>(
      (table, row) => table.concat(row.split(",").map((c) => c.trim())),
      []
    );
}

var peopleTable = lameCSV(peoplsCSV);
// [
//     ['Name','Age','Hair'],
//     ['Merble','35','red'],
//     ['Bob','64','blonde'],
// ]

function selectNames(arr: Table) {
  const [, ...rest] = arr.map(first);
  return rest;
}

function selectAges(arr: Table) {
  const [, ...rest] = arr.map(second);
  return rest;
}

function zip<T extends Array<unknown[]>>(...args: T): T[number][] {
  const longestLength = Math.max(...args.map((arg) => arg.length));

  const arrayOfIndexes = Array.from(Array(longestLength).keys());

  return arrayOfIndexes.map((i) => args.map((array) => nth(array, i)));
}

var zipped = zip(selectNames(peopleTable), selectAges(peopleTable));

console.log(zipped);
// [["Merble","35"], ["Bob","64"]]
```

With that, we have officially modernized all the examples from chapter 1. I hope you enjoyed this series so far and that you will keep looking out for future posts on this.
