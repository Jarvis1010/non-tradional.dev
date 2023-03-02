---
title: "JavaScript Operators And How to Use Them"
datePublished: Mon Sep 23 2019 13:26:02 GMT+0000 (Coordinated Universal Time)
cuid: ckwx9xisz0chjuws1gmwb44p4
slug: javascript-operators-and-how-to-use-them-769ce56d1538
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316733923/mSKPRXfHH.jpeg
tags: javascript

---

Photo by [Dan Lohmar](https://unsplash.com/@dlohmar?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

JavaScript, like most programming languages, provide built-in operators to assist developers when writing code. Operators can be thought of as a sort of built-in function provided by the language. It’s important to not just understand how to use them, but to know which operators are available.

JavaScript operators can be grouped in a few groups: Arithmetic, String, Assignment, Comparison, Type, and Bitwise operators. In this post, I will go over all the operators except the bitwise operators. Bitwise operators are unique operators that treat their operands as 32-bit numbers. There is a great [MDN article about bitwise operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators) if you want to know more.

### Type Operators

There are two type operators. `typeof` is used to get the data type of any value and the `instanceof` returns true if an object is an instance of an object type.

### Arithmetic Operators

JavaScript provides several operators to help you work with numbers. There are your 4 basic arithmetic operators:

- `+` adds two number values
- `-` subtracts two number values
- `*` multiply two number values
- `/` divides two number values

As of ES2015, a new operator was added, `**` or the exponentiation operator. So it is important to notice the difference between these scenarios:

```javascript
2 * 3 // 6

2 ** 3 //8

2 * * 3 // Uncaught SyntaxError: Unexpected token *
```

It’s also important to note that these operators follow the standard order of operations, so just think back to your 7th grade pre-algebra teacher to Please Excuse My Dear Aunt Sally (PEMDAS) and you should be fine.

In addition to these, there are a few other arithmetic operators that are important to know. The `%` is called the modulus operator. The modulus operator performs remainder-division. These will return the remainder after dividing the first number by the second. So `4 % 3` is `1` since 4 divided by 3 has a remainder of 1. In practical usage, it allows us to keep any number in the range of `0` to `n-1` where n is the second value in the modulus operation, like this:

```javascript
0 % 3; // 0
1 % 3; // 1
2 % 3; // 2
3 % 3; // 0
4 % 3; // 1
5 % 3; // 2
6 % 3; // 0
```

In addition to those, there are the increment and decrement operators, or `++` and `--` respectively. A pattern that is so common in programming is to increment or decrement something like this `x = x + 1` and this operator gives us a shorter syntax for it. One can do that same thing by simply using the increment operator like this: `x++`

The increment and decrement operators can either be prefixed or suffixed and this can be very important, depending on how your code is written. The most important thing to remember is that it is read left to right. `x++` means get the value of x and then increment x. `++x,` on the other hand, means that x is incremented first and then the value is assigned. For example:

```javascript
const logNum = (num) => console.log(num);

let x = 0;

logNum(x++); // 0

logNum(x); // 1

logNum(--x); // 0
```

### Assignment Operators

JavaScript has a basic assignment operator `=` that lets you assign a value to a variable, like this `x = 45`. It is important to remember that `=` is not used for comparing values, it is for assignment only. In addition to the basic assignment operator, there are arithmetic assignment operators. It creates a shorter syntax to doing arithmetic on a variable and assigning that new value to the same variable. For example:

```javascript
x += 5; // same as x = x + 5;
x -= 5; // same as x = x - 5;
x %= 5; // same as x = x % 5;
x **= 5; // same as x = x % 5;
```

### String Operators

String has a concatenation operator `+` and a concatenation assignment operator `+=.` For example:

```javascript
const hello = "Hello";
const world = "World";
const greeting = hello + ", " + world;
const greeting += "!";

console.log(greeting); // Hello, World!
```

As you can already see, it is the same operator as the addition and the addition assignment operators, but when used with strings, it concatenates the strings together. So the obvious next question is what happens if you try to use it with a number and a string? There is a big long explanation why, but to put it simply, the string always wins. So you can have a situation like this:

```javascript
const numArr = [1, 2, "3", 4, 5];

let sum = 0;

numArr.forEach((x) => (sum += x));

console.log(sum);
// 3345
```

This situation happens because the moment you use the `+` with a string, JavaScript will coerce the values to strings and concatenate them.

### Logical Operators

There are three logical operators: the logical and, logical or, and logical not or `&&` `||` and `!` respectively. Logical operators are typically used with boolean values and expressions, but as was shown in my post about [conditional statements](https://medium.com/the-non-traditional-developer/how-to-make-your-conditional-statements-easier-to-read-in-javascript-6e8c3f5e44f6) that they can be used with any value. `||` returns the first truthy value or the last value whichever comes first. `&&` returns the first falsey value or the last value whichever comes first.

The not operator simply flips the bit on a boolean expression and turns a true to false and vice versa, like this:

```javascript
!true; // false
!false; // true
```

Just like the other logical operators, if you use them with non-boolean values, the value will be coerced to a boolean first before the bit it switched. The coercion follows the JavaScript truthy/falsey rules.

### Comparison Operators

The final set of operators are the comparison operators. Comparing things in JavaScript deserves an entire article, [which MDN has already written](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators). To put it simply, equality operators come into two varieties: with and without strict type checking. So, `1 == '1'` will return true, but `1 === '1'` will return false. The inverse operators also exist, `!= and !==` checks for not equality.

Besides checking for equality, you can also check if a value if greater than, less than, or greater than/less than or equals using `> < >= <=` respectively. Once again, outside of numbers, there are some interesting nuances on how this works and you should refer to the MDN article above.

There are several operators in JavaScript. Mastering their use will help you as you solve problems and create logic that your JavaScript applications need.
