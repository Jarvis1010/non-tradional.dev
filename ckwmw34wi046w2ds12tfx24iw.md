---
title: "Converting Object Oriented Code to Functional in JavaScript"
datePublished: Mon Nov 29 2021 19:55:14 GMT+0000 (Coordinated Universal Time)
cuid: ckwmw34wi046w2ds12tfx24iw
slug: converting-object-oriented-code-to-functional-in-javascript-f3d50cd06d93
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316794769/w-MYhKrH_r.jpeg
tags: javascript, data-structures, functional-programming, object-oriented-programming

---

Photo by [Linus Nylund](https://unsplash.com/@dreamsoftheoceans?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

JavaScript is an interesting language. It doesn't fit nicely in any one Paradigm. JavaScript does have characteristics of Object-Oriented languages, but it also has attributes of Functional programming languages and doesn't strictly follow either style perfectly.

Recently I was looking at some primary computer science material and was reading about data structures, specifically, [the "Stack" data structure](https://medium.com/basecs/stacks-and-overflows-dbcf7854dc67). I noticed that all the examples tend to be object-oriented, and I wondered why other language paradigms were not shown.

That was when I decided to think about what a Stack would look like using other programming paradigms. So that is what we will try to do in this post.

(To keep the code universally readable to all readers here, I am only using JavaScript and not Typescript)

First, let's look at how I would implement a Stack using pure OO.

```javascript
class Stack {
  #head;
  push(value) {
    const node = {
      value,
      next: this.#head,
    };
    this.#head = node;
  }
  peak() {
    return this.#head?.value;
  }
  pop() {
    const safeNode = this.#head ?? {};
    const poppedValue = safeNode.value;
    this.#head = safeNode.next;
    return poppedValue;
  }
  [Symbol.iterator] = function* () {
    let safeNode = this.#head ?? {};
    while (safeNode.value) {
      yield safeNode.value;
      safeNode = safeNode.next ?? {};
    }
  };
}

const stack = new Stack();
stack.push(1);
stack.push(2);
stack.peak(); // 2
stack.pop(); // 2
stack.peak(); // 1
stack.push(3);
stack.push(4);
stack.push(2);
stack.push(2);
console.log([...stack]); // [ 2, 2, 4, 3, 1 ]In the above code, we are defining a Stack class that has a private property called #head and three methods called push , pop , and peak that allows you to add an item to the stack, take an item off, and look at the top item on the stack.
```

Our `Stack` is implemented using a linked list. If you are unfamiliar with linked lists, I recommend [checking out the basecs article on linked lists](https://medium.com/basecs/whats-a-linked-list-anyway-part-1-d8b7e6508b9d) but summarizing: A linked list is a list of objects called nodes. Each node has two properties: a value and a reference to the next node. To traverse a linked list, you start at the first node, called the head, and keep going to the next node until you run out of nodes.

Our Stack keeps track of the head node in the private `#head` property. When we call `peak` it returns the value at the head node. When we call `push`, it will create a new node with the value provided, assign the node at the `#head` property to `next` and assign that new node to the`#head` property. Finally, when we call `pop`, we pull the value off the`#head` property, and then assign the value of `next` to the`#head` property.

We are also making our `Stack` iterable by assigning a generator function to the`[Symbol.iterator]` property. [You can learn more about generator functions in my blog post on them](https://non-traditional.dev/how-to-code-the-fizz-buzz-challenge-using-javascript-generators-ed7f9f9c18af), but simply put, this allows JavaScript to iterate over the items in the stack, and we can do things like spreading them into an array or using them in a `for...of` loop.

To use the stack, we call `new Stack()` and assign that to a variable. From there, we can do all the fun things to a stack you can. This is probably the most common way you will see a `Stack` implemented.

The first step to making this more functional is to stop mutating our `Stack` every time we call either the `push` or `pop` method. So let's make a new version called `ImmutableStack`:

```javascript
class ImmutableStack {
  #head;

  constructor(node) {
    this.#head = node;
  }

  push(value) {
    const node = {
      value,
      next: this.#head,
    };

    return new ImmutableStack(node);
  }

  peak() {
    return this.#head?.value;
  }

  pop() {
    const safeNode = this.#head ?? {};

    return new ImmutableStack(safeNode.next);
  }

  [Symbol.iterator] = function* () {
    let safeNode = this.#head ?? {};
    while (safeNode.value) {
      yield safeNode.value;
      safeNode = safeNode.next ?? {};
    }
  };
}

let stack = new ImmutableStack();

stack = stack.push(1);
stack = stack.push(2);
stack.peak(); // 2
stack = stack.pop();
stack.peak(); // 1

stack = stack.push(3);
stack = stack.push(4);
stack = stack.push(2);
stack = stack.push(2);

console.log([...stack]); // [ 2, 2, 4, 3, 1 ]
```

Let's talk about what we changed. We still have a private `#head` property. Also, our `peak` and iterator or also still the same. What we changed was that we added a `constructor` to our class. The `constructor` allows us to initialize values in our `Stack` when we call `new Stack()` in our code. Our constructor lets us optionally pass in a node and assign it to the`#head` property. This will allow us to initialize our `Stack` with an existing linked list if we want.

In our `push` method, we still take in a value, assign it to a new node, and assign the value at the `#head` property to the `next` property of that new node. However, instead of changing the value of the `#head` property, we are returning a new `Stack` initialized with the new node. The same thing happens in our `pop` method. We are returning a new `Stack` initialized with the next node of our stack.

I think it is essential to stop and reiterate why this is helpful. If two parts of our code were looking at that same `Stack` and one part called `push` or `pop` on it. The other part of our code would not know that the value was changed on it. This can lead to unpredictable behavior. Making our Stack immutable ensures that we can write our code and be confident that some other part of our code won't change its values underneath us.

The next evolution into a more functional style is to move away from creating classes at all and instead define methods that take inputs and return values:

```javascript
const stackUtils = {
  push(head, value) {
    const node = {
      value,
      next: head,
    };

    return node;
  },

  peak(head) {
    return head?.value;
  },

  pop(head) {
    const safeNode = head ?? {};

    return safeNode.next;
  },

  getIterator(head) {
    return (function* () {
      let safeNode = head ?? {};
      while (safeNode.value) {
        yield safeNode.value;
        safeNode = safeNode.next ?? {};
      }
    })();
  },
};

let stack;

stack = stackUtils.push(stack, 1);
stack = stackUtils.push(stack, 2);
stackUtils.peak(stack); // 2
stack = stackUtils.pop(stack);
stackUtils.peak(stack); // 1

stack = stackUtils.push(stack, 3);
stack = stackUtils.push(stack, 4);
stack = stackUtils.push(stack, 2);
stack = stackUtils.push(stack, 2);

console.log([...stackUtils.getIterator(stack)]); // [ 2,2,4,3,1 ]
```

In the above code, instead of creating a class, we define an object with utility methods that know how to peak, push, and pop values on a linked list. Since our methods no longer have access to a head property, that value must be passed in as an argument to our methods. For that same reason, we also have created a function `getIterator` that will generate an iterator for us to use in those circumstances.

Other than that, our code works pretty much the same way as our `ImmutableStack`. The only difference is that we must pass the `stack` variable into every method call.

The above example already follows a functional style, but we can go to one more level of inception. So let's show you that code first:

```javascript
//stackUtils.js

export function peak(head) {
  return head?.value;
}

export function pop(head) {
  const safeNode = head ?? {};

  return safeNode.next;
}

export function push(head) {
  return (value) => {
    const node = {
      value,
      next: head,
    };
    return node;
  };
}

export function getIterator(head) {
  return (function* () {
    let safeNode = head ?? {};
    while (safeNode.value) {
      yield safeNode.value;
      safeNode = safeNode.next ?? {};
    }
  })();
}

// index.js

import { push, pop, peak, getIterator } from "./stackUtils.js";

let stack;

stack = push(stack)(1);
stack = push(stack)(2);
peak(stack); // 2
stack = pop(stack);
peak(stack); // 1

stack = push(stack)(3);
stack = push(stack)(4);
stack = push(stack)(2);
let stackPusher = push(stack);

stack = stackPusher(2);

console.log([...getIterator(stack)]); // [ 2, 2, 4, 3, 1 ]
```

First, it is more common in FP languages to group related functions in a module, so that was the first thing we did. We move all the methods into simple functions that we export from the`stackUtils.js` file.

Generally speaking, all the functions work the same way except for the `push` function. A common functional programming pattern is only one parameter per function. If you need more than one argument, you use a pattern called *"currying."* When you curry functions, your function will return a new function that requires the next argument. You keep doing this until you no longer need any arguments.

In our `push` function. We take an argument called `head` and return a new function that takes an argument called `value.` When we call that second function, it will still have access to that `head` value and can complete the code we need to run.

One of the things this gives us is the ability to do "partial application." On one of the lines near the end, we create a new function `stackPusher,` that will take a value and push it onto a new stack.

This gives us two things we can do. We may know which stack we want to push onto in one part of our code, but we don't yet know the value. Instead of trying to keep track of the stack in some global store, we can pass around a function that already knows which stack to use and needs to have a value passed in:

```javascript
const stackPusher = push(stack)

/* later in our code */

const newStack = stackPusher(5)
```

This can be helpful if we want to create multiple stacks with the same tail but with different heads. We can create a function from the push function that we can use to generate multiple lists with the same tail using different values:

```javascript
const stackPusher = push(stack)

const stack1 = stackPusher(5)  
const stack2 = stackPusher(2)  
const stack3 = stackPusher(3)  
const stack4 = stackPusher(8)  
const stack5 = stackPusher(12)
```

So there are four styles of code that you can use to do the same thing. One is a typical OO style, and the last is a distinctive FP style with all the variations in between. I recommend trying all these styles out and seeing which makes the most sense.