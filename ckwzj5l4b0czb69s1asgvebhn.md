---
title: "Arrow Functions vs Traditional Functions in JavaScript"
datePublished: Fri Jun 14 2019 21:55:09 GMT+0000 (Coordinated Universal Time)
cuid: ckwzj5l4b0czb69s1asgvebhn
slug: arrow-functions-vs-traditional-functions-in-javascript-8ff1a48ede12
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316671482/xvo3CPcq1.jpeg
tags: javascript

---

Photo by [Marija Zaric](https://unsplash.com/@simplicity?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

For the longest time, JavaScript only had two ways to declare functions. You declared them using the `function` keyword or by using it as a function expression and assigning it to a variable and invoking it from there. For example, to write a function that doubled a number passed in could look like this:

```javascript
function square(a) {
  return a * a;
}

// or

var square = function (a) {
  return a * a;
};

square(2); //returns 4
```

These function declarations worked fine, for the most part, but had some “gotchas” that could result in some unintentional things happening in your code if you didn’t plan on it.

One of those gotchas is what is typically call “hoisting”. In JavaScript, functions and variable declarations are hoisted to the top of the scope. The concept of scope and context in JavaScript deserve an entire post of its own, but simply put they would be lifted to the top of the closest function declared in. This means code declared before the function or variable can use them. For example, If one were to write code like this code:

```javascript
greet("Tom"); // "Hello Tom"

function greet(name) {
  return "Hello " + name;
}
```

The code will still work because JavaScript will hoist the function declaration up to the top of the scope, which in this case is the global scope.

This is only part of the gotcha. For variables declared with `var` are also hoisted, BUT the assignment of the variables are not. So if we were to change the above example to an anonymous function assigned to a variable, we have an interesting change in the outcome:

```javascript
greet("Tom"); // Uncaught TypeError: greet is not a function

var greet = function (name) {
  return "Hello " + name;
};
```

This is because the above code, is effectively changed to be like this after hoisting:

```javascript
var greet;
greet("Tom"); // greet is not a function

greet = function (name) {
  return "Hello " + name;
};
```

You can see that your function is or is not made available through hoisting depending on how you are using the function, which can be very unintuitive, especially for less-experienced developers.

The other gotcha has to do with the `this` keyword. Once again the `this` keyword deserves a whole series of posts devoted to it and all its nuances. But here is an example of how confusing it can be:

```javascript
function logThis() {
  console.log(this);
}

console.log(this); //window

logThis(); // window

new logThis(); //logThis
```

This, no pun intended, is where things can start getting very tricky, especially as the functions are passed into other contexts such as in callbacks. For this reason, JavaScript has the ability to use `call`, `bind`, or `apply` to control the ‘context’ that is applied to it, which you can learn more about it [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function#Methods).

With ES6, a new syntax for declaring functions was introduced: The Arrow Function (they are also sometimes called fat arrow functions or lambdas). The most obvious change that it introduced was an updated and flexible syntax. On the surface, it would appear that we replaced `function(){}` with `()=>{}` but it is much more than that. The Arrow function syntax allows one to minimize how much you write. Taking the double function above we can now write it in the following ways:

```javascript
//square function function
square(a) {
 return a * a
}

//square function expression
const square = function(a){
 return a * a;
}

//using arrow syntax
const square = (a) => {
 return a * a;
};

//implicit Return
const square = (a) => a * a;

//no parameter parenthesis
const square = a => a * a;
```

This is not all that arrow functions give you. Arrow functions do not bind their own `this` and are always anonymous. Arrow functions also cannot be used as constructors in JavaScript. Arrow functions get their `this` from their Lexical context they were declared in. This means that arrow functions have a much more intuitive feel when being passed around into other contexts. I think it is also important to note, given our topic of hoisting from above, that in ES6, `let` and `const` were added as ways to define variables and they are not hoisted like the keyword `var`.

### Which one do you choose?

The simple answer is that one should use the appropriate syntax for the situation. How do you determine appropriate? Some of that is subjective and some isn’t but there are some general guidelines. One is that you should favor Arrow functions over regular functions. Arrow functions give you more flexibility to move from minimal to verbose where traditional JavaScript functions only have one form: verbose. Arrow functions are also more intuitive regarding scope and `this`. They are preferred when passing a callback.

Traditional JavaScript functions do have their place though. The fact that they are hoisted, for example, means you can organize your files by what is most important and let the hoisting ensure that the function is available at the right time.

Understanding how functions work in JavaScript is extremely important. Now that there is more than one way to declare a function, understanding the nuance difference between them is even more important as one determines which function syntax to use.
