---
title: "How to Make Your Conditional Statements Easier to Read in JavaScript"
datePublished: Wed Sep 18 2019 13:26:01 GMT+0000 (Coordinated Universal Time)
cuid: ckwzej4gr0cdhb6s1656u04jk
slug: how-to-make-your-conditional-statements-easier-to-read-in-javascript-6e8c3f5e44f6
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316686765/shKojxgXT.jpeg
tags: javascript

---

Photo by [Jon Tyson](https://unsplash.com/@jontyson?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the most powerful tools that all programming languages provide is the ability to conditionally run code, creating branches of code that runs only when certain conditions are met. In JavaScript, as in many other languages, this is called conditionals. JavaScript has 3 primary types of conditionals: If/else blocks, switch statements, and conditional expressions. These 3 tools can help you write some very awesome code.

Just like anything, conditionals done wrong can also lead to some very complicated code that is hard to follow and even harder to maintain. Following some good principles, you can use these conditionals to your advantage.

### The Basics of JavaScript Conditionals

As I said earlier, JavaScript has 3 primary types of conditionals. The first is the if/else block. The basic of the if statement is very simple: if the condition is true, the code block will be run. For example:

```javascript
if (condition) {
  // code that will be run only if condition is true
}
```

If blocks also can create two blocks of code, based on the same condition by adding the `else` keyword, like this:

```javascript
if (condition) {
  // code that will be run only if condition is true
} else {
  // otherwise this code will run
}
```

You can also add more conditions using else/if like this:

```javascript
if (condition) {
  // code that will be run only if condition is true
} else if (condition2) {
  // otherwise this code will run
  // if condition2 is true
} else {
  // otherwise this code will run
}
```

The switch statement, on the other hand, checks the value of one or more cases against an expression. If none of the cases match, then you can provide a default case.

```javascript
switch (expression) {
  case `value1`: {
    // code executed if expression matches value1
    break;
  }
  case `value2`: {
    // code executed if expression matches value2
    break;
  }
  default: {
    // code executed if nothing matches
    break;
  }
}
```

The important gotcha for switch statements is that unless you either `break` or `return` in the case statement, the code will continue to “fall through” and execute the code in other case statements. For example:

```javascript
const name = "Jim";

switch(name){
   case "Mark": {
     // This code will not run
   }
   case "Jim": {
     // This code and all other code after will run
   }
   case "Sarah": {
     ...
   }
   default: {
     ...
   }
}
```

The final conditionals are conditional expressions. These are called expressions, because unlike if blocks and switch statements, an expression evaluates to a value and can be used anywhere a value would be used or assigned. The one most familiar to those of other languages would be the ternary. The ternary is expressed by giving a condition followed by a `?` operator and then the value if true and the value if false seperated by a `:` For example:

```javascript
const myValue = condition ? valueIfTrue : valueIfFalse;
```

Because ternaries evaluate to values, you can nest ternaries inside of ternaries (though you probably should avoid doing it), like this:

```javascript
const myVal = condition ? valTrue : condition2 ? valTrue2 : valFalse;
```

The other form of conditional expression is called a logical assignment. Logical assignment uses JavaScripts logical operators, `&&` and `||` , to make assignments based on two or more value’s truthyness and falsyness. To learn more about [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) and [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy), check out these two great MDN articles.

Assignments using the `&&` will look for the first falsy value or the last value in the chain, whichever comes first. Assignments using the `||` will look for the first truthy value or the last value in the chain, whichever comes first. For example:

```javascript
const myVal = falsyVal || truthyVal || otherVal;
//truthyVal

const myVal = truthyVal && falsyVal && otherVal;
//falsyVal
```

You can chain and mix and match as the operators as much as you want, but honestly, that is inadvisable. It is probably best to evaluate two values and no more than three.

### Best Practices Using Conditionals

If it hasn’t become apparent from the above explanations, conditionals can be powerful tools as well as powerful hindrance in your code. To fight this, there are a few principles that you should follow to keep your conditionals from becoming complicated spaghetti code.

1. Don’t use If blocks to conditionally assign values. Why do this:

```javascript
let myVal;
if (condition) {
  myVal = valueIfTrue;
} else {
  myVal = valueIfFalse;
}
```

when you can do this:

```javascript
const myVal = condition ? valueIfTrue : valueIfFalse;
```

2. Nested If blocks should be avoided at all costs. By the way, else/if is technically a nested if block. We think we are writing this:

```javascript
let result;
if (condition) {
  // calculate result_v1
} else if (condition2) {
  // calculate result_v2
} else {
  // default calculation
}
return result;
```

What we are really writing is this:

```javascript
let result;
if (condition) {
  // calculate result_v1
} else {
  if (condition2) {
    // calculate result_v2
  } else {
    // default calculation
  }
}
return result;
```

Anytime you have a nested if statement, chances are you can rearrange the logic and remove the nesting. The above could be rewritten as:

```javascript
if (condition) {
  // return calculated result_v1
}

if (condition2) {
  // return calculated result_v2
}

// return default calculation
```

The latest version is the same logic but it is much easier to wrap your head around and therefore easier to refactor or add more logic later.

3. Switch statements are not bad. Switch statements have got a bad rap and some will go to great lengths to avoid them, but sometimes switch statements provide the cleanest code. The refactored code above is very much like a switch statement and could be rewritten like this:

```javascript
switch (expresion) {
  case one: {
    // return calculated result_v1
  }
  case two: {
    // return calculated result_v2
  }
  default: {
    // return default calculation
  }
}
```

Some would say that switch statements violate the Open/Closed principle of Object-Oriented Programming and I would counter that the switch statement doesn’t violate it, your use of the switch statement does. If the switch statement makes the most readable and maintainable code don’t avoid it because someone else says they are bad.

4. Else statements tend to add unnecessary complications to your code logic. A developer I respect a lot, Kyle Shevlin, had this “lukewarm” take:

%[https://twitter.com/kyleshevlin/status/1131674209287979008?s=20]

I echo this sentiment. As you have already seen, by avoiding the else statements above, a simpler and easier to follow logic manifested itself. To get further examples I would recommend you check out [his blog post on refactoring a complicated, nested if statement.](https://kyleshevlin.com/when-elses-make-your-code-worse)

Conditionals are extremely powerful tools in code. Just like a sharp knife, if used in the wrong way conditionals can hurt you. Used in the right way, you can write some amazing code that solves some fantastic problems.
