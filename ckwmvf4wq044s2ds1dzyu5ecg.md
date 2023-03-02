---
title: "Let, Const, and Var: A Beginner’s Guide to Variable Declaration in JavaScript"
datePublished: Tue Sep 10 2019 13:31:01 GMT+0000 (Coordinated Universal Time)
cuid: ckwmvf4wq044s2ds1dzyu5ecg
slug: let-const-and-var-a-beginners-guide-to-variable-declaration-in-javascript-3486d6a4dfd5
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316800349/GB0of-sum.jpeg
tags: programming-blogs, javascript, codenewbies

---

Photo by [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

An important feature of any programming language, including JavaScript, is the ability to store data in variables that our code can access later on in its execution. For years, JavaScript only had one way to declare variables: using the `var` keyword. Starting in ES2015 (a.k.a ES6) two new keywords were added to the spec that gives JavaScript not just more variety, but more predictability when using these variables. These keywords are `let` and `const` and have forever changed the way we write JavaScript

There is more to these three keywords than just declaring variables. Each one is treated differently depending on various circumstances. I thought it would be helpful to go over those nuances in this post.

### JavaScript Types

Before we get into how to declare variables, I believe it is important to have a brief conversation on the JavaScript type system. No matter what some developers might try to tell you, JavaScript **DOES** have a type system. JavaScript’s type system is considered a “weak” type system and more specifically, a dynamic type system. When people say that JavaScript doesn’t have a type system, what they are really saying is that JavaScript is not “strongly” typed or, in other words, does not provide type safety.

There is a very [good explanation on the differences between them at Wikipedia](https://en.wikipedia.org/wiki/Strong_and_weak_typing) but in an attempt quickly summarize it: a strongly typed language, through various measures, will at least minimize, if not eradicate all errors caused by using the wrong data type where weakly typed languages do not and force the developer to write defensive code to protect themselves against type errors at run time.

This is important to know for the rest of this post because data types and variables go hand in hand and having at least a basic understanding of JavaScript’s type system is very important when talking about declaring variables.

(If you would like to write JavaScript in a strongly typed way, you can use TypeScript. TypeScript is a superset of JavaScript that transpiles back to vanilla JavaScript. Ultimately, JavaScript is still weakly typed at run time, but it adds a layer of safety at development time that can help you feel as the developer feel more confident in your code.)

### How To Use Var, Let, and Const

As I said before, there used to be only one way to declare variables in JavaScript by using the keyword `var.` Here is an example of the minimum you need to declare a variable:

```javascript
var age;
```

In the above example, we have declared a variable called `age` which means we told our code “Hey, I need someplace to store some data, I’ll tell you what it is later.” At this point, the code still doesn’t know what type the data will be all it knows is that you need some space and you want to call that space `age` when you use it.

One might ask, what would happen if we tried to use the variable after we declare it. Would it have a value? The answer is yes, it would have a value:`undefined.` This is because JavaScript when setting that variable aside puts a place holder value until you assign a value to it. That value is `undefined,` which is a specific JavaScript value and not just JavaScript’s way of saying it hasn’t been defined yet.

Now that we have the space, we can then use that space to save data. Here is an example of each of those steps:

```javascript
var age;

console.log(age); // undefined

age = 12;

console.log(age); // 12
```

Now just because we assigned a number to our variable age, doesn’t mean it can’t be reassigned to another value and that value can be of any type. This is where JavaScript’s dynamic typing kicks in. It allows us to do stuff like this:

```javascript
var age;  
age = 12;  
age = age + 2;  
age = "Hello, World!";  
age = false;  
age = null;  
age = undefinded;
```

We will talk more about this later, but basically JavaScript gives us enough freedom to shoot ourselves in the foot.

Before we go into the other two ways to declare variables, I think it is important to go over some other variations on declaring variables. The first is that you can both declare a variable and instantiate with a value at the same time, like this:

```javascript
var age = 12;
```

The second is that you can declare as many variables in a single line as you want simply by separating each variable with a comma. One can instantiate a value or not, like this:

```javascript
var age = 12, name, isOld = true;
```

In Modern JavaScript, we now have two additional ways to declare variables using either the `let` or `const` keywords. `let` is very similar to `var` in that, it lets you declare a variable and optionally instantiate it with a value. You can also do more than one at a time, just like var. We can rewrite the above example like this:

```javascript
let age = 12, name, isOld = true;
```

The main difference between `var` and `let` is how they are impacted by scope and the unique JavaScript concept of hoisting, which we will go more into that in the next section.

The other keyword is `const` which is short for constant. `const` is unique in two ways. Firstly, you **MUST** instantiate a variable with a value when you use `const` and secondly, you cannot reassign the variable to a new value afterward. `const` also has the same scope rules as `let`

### How Does Scope Impact Variables

Scope in JavaScript deserves a blog post of its own, but to quickly summarize: scope is kinda like an environment separated from the rest of the code. Variables declared in a scope are only available in that scope. Scope can be declared inside of another scope. A child's scope has access to the parent scope, but the parent does not have access to the child's scope nor to any of the sibling scopes if they exist.

What is considered scope depends on which keyword you use. Variables declared using `var` are considered to use the execution scope. The execution scope is typically the enclosing function, otherwise the global scope. This means that a variable declared using `var`inside a block, such as an if-block, are still available outside of the block like this:

```javascript
function varTest(){  
   if(true){  
     var age = 12;  
   }  
   console.log(age) //12  
}  
console.log(age) //Throws Reference Error in strict mode
```

As you can see, despite age not being declared until inside the if block, it is still available after, but only within the body of the function.

`let` and `const` are block-scoped. Generally speaking, anything inside two curly braces`{}` is considered a block. Here is an illustration of that scope:

```javascript
function letTest() {  
  let age = 12;  
  {  
    let age = 2;   
    console.log(age);  // 2  
  }  
  console.log(age);  // 12  
}
```

The other important difference is that variables declared with `var` are hoisted, but variables declared with `let` or `const` are not. Hoisting is a phenomenon that variable declarations, but not assignments are ‘hoisted’ to the top of the scope. Going back to our `varTest` function, it can be rewritten like this:

```javascript
function varTest(){  
   console.log(age) //undefined  
   if(true){  
     var age = 12;  
   }  
   console.log(age) //12  
}

//the above is effectively this

function varTest(){  
   var age;  
   console.log(age) //undefined  
   if(true){  
     age = 12;  
   }  
   console.log(age) //12  
}
```

Despite the fact that the variable is not declared until the if block, JavaScript ‘hoists’ up the declaration to the top of the scope, the function body. Variables declared using `let` or `const` are not hoisted, even inside the block scope, and are only available after it has been declared in the code.

### What Are Some Best Practices Regarding Variable Declaration

When declaring something as best practice, there are many opinions. There are, however, common things that most agree upon. First of all, you should no longer use the`var` keyword to declare variables due to the less than intuitive way that JavaScript handles the declarations of those variables.

Second, unless you need to reassign a value to the variable, you should use `const` to declare variables. There is no technical benefits for using `const` over `let` except one important thing. It prevents the developer from reassigning a new value to the variable, ensuring that the value and data type stay the same and can be reliable to be the same.

Variables is an import part of anyone’s JavaScript code. Understanding the various ways to declare variables and why you use them in your code, will help you write code that is easier to maintain.