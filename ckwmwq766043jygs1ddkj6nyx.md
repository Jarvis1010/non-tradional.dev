---
title: "Safely Extending The JavaScript Set Object Using Proxies"
datePublished: Mon Nov 25 2019 14:31:02 GMT+0000 (Coordinated Universal Time)
cuid: ckwmwq766043jygs1ddkj6nyx
slug: safely-extending-the-javascript-set-object-using-proxies-3ce25702b8c3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316791742/1arOyiz-r.jpeg
tags: proxy, programming-blogs, javascript

---

Photo by [Anne Nygård](https://unsplash.com/@polarmermaid?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

In 2015, one of the most important changes to JavaScript occurred: The release of ECMAScript version 6 or ES6. Features that had been on hold for ages finally made it into the JavaScript specification. The changes were so impactful, that we are feeling the impact of those changes even all these years later.

Included in all these changes were two new data structures:`Map` and `Set.` Prior to ES6, JavaScript only had two native data structures, the `Object` and the `Array.` These two structures have served the community well, but they have been lacking some important features.

For this post, I want to focus on the `Set` obj. Prior to ES6, JavaScript did not have an easy way of maintaining a unique list of items. With arrays, there is no built-in way to guarantee that all values are unique. The only way to guarantee uniqueness in an array is to write code that maintains that uniqueness when the values are added to the array.

Objects, on the other hand, do have guaranteed unique keys. Unfortunately, Object keys do not maintain the original order that they were added to the object. Keys also can only be strings or at least convertible to a string. This means if you need a unique list of Objects, you can’t use Object property keys to guarantee that for you.

Luckily, `Set` was added to the ECMAScript Spec. A `Set` is a data structure that is iterable like an Array, but it also guarantees both uniqueness and the order the value was added to the Set.

The Set object has some very intuitive properties and methods that let you work with it like `add` which is similar to `push` on arrays, as well as `delete` and `clear` which lets you remove values. There is also the `has` method that allows you to verify if a value is already in the Set or not. You can check out the full list of methods and properties at [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set).

Despite how awesome it is to have a proper Set data structure, the JavaScript implementation is still lacking some really nice methods found in other languages. Most languages implement functions that do things like union two Sets together or finds the intersection between two unions, but JavaScript doesn’t currently support those methods natively.

We could build some helper functions, but it’s not quite the same as having static methods on the Set object itself. We could implement our own Structure but now we are just recreating the wheel, especially since it’s typically best to use native objects whenever possible because they are implemented in a lower-level language in the browser and typically work better.

What we could implement the functions on the `Set.prototype` which would make it so all our Set Objects had these functions natively, but it is very dangerous to mess with prototypes of JavaScripts Native objects. Luckily, ES6 added a new feature called Proxies.

### What Are Proxy Objects

A Proxy is an object that wraps another object or a function. This Proxy Object allows one to define custom behavior for fundamental operations such as getting and setting property values.

The Proxy constructor is very simple to create it takes a target object or function and handler object used to define the custom behavior that you are trying to Proxy. Like this:

const proxiedObj = new Proxy(target, handler);

As you may have guessed at this point, the handler is where all the magic happens. The proxy handler is simply a JavaScript object where you can define “traps”, or in other words functions that will be called in place of regular JavaScript functionality.

This handler doesn’t define traps for specific properties of an object. These traps are much more general. For example, you can create a trap when getting or setting property values, getting or setting the prototype, and many other things you typically do. In these traps, you can add custom logic that is different than the typical JavaScript functionality. You can get a f[ull list of all the traps that can be defined at MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler).

For example, when accessing a property value of an object that hasn’t been defined yet, the default functionality is to return the value `undefined.` If we wanted to change that functionality we could write a handler like this:

```javascript
const handler = {
   get:(target, name) => target[name]
                          ? target[name]
                          : `The property, ${name}, doesn't exist`;
 }

const person = {
    fullName: "Bob Newhart",
    age: 81
}

const proxyPerson = new Proxy(person, handler);

proxyPerson.age // 81
proxyPerson.lastName // "The property, lastName, doesn't exist"
```

As you can see, when on attempts to get a property from the proxy wrapped object, it will instead call the “get” trap (if there is one) on the handler object, passing in the target object and the property name as arguments to the function. In our trap, we first check if the name is on the target object and return it’s value if it is. Otherwise, we return a template literal stating that the name doesn’t exist.

Using this knowledge, we can now build our custom “Set” object extended with some custom functions. Since the intention of our new Set object is not to override the core functionality of the Set object, we need to handle it when a core property or method is being requested. We may try to handle it like this:

```javascript
const handler = {
  get: (target, name) => {
    switch (name) {
      default:
        return target[name];
    }
  },
};

const mySet = new Proxy(new Set(), handler);
```

The problem is that if the property requested is a method, it can’t simply be returned. It needs to be bound to the target first, otherwise, the method will be called with the wrong scope. So we can update the handler to look like this:

```javascript
const handler = {
  get: (target, name) => {
    switch (name) {
      default: {
        return typeof target[name] === "function"
          ? target[name].bind(target)
          : target[name];
      }
    }
  },
};

const mySet = new Proxy(new Set(), handler);
```

With the above update, we will correctly return a function properly bound to the underlying target object. Now, all we need to do is add our extension methods. For brevity, I’m only going to implement one method, but you can see a [more complete implementation at my code sandbox](https://codesandbox.io/s/my-set-liwcj).

Let’s implement a Union function. This function will take in a Set and return a new Set with all the values of both Sets, with no duplicate values of course. Here is one way to implement this:

```javascript
const handler = {
  get: (target, name) => {
    switch (name) {
      case "unionWith": {
        return (set) => new Set([...target.values(), ...set.values()]);
      }

      default: {
        return typeof target[name] === "function"
          ? target[name].bind(target)
          : target[name];
      }
    }
  },
};

const mySet = new Proxy(new Set(), handler);
```

Now if someone requests a `unionWith` method on our new Set object, it will return a function takes a Set object and returns a new Set object with all the values combined from both Set objects.

So things are going well, but it’s still not in an optimal state. For a regular Set object, we call it with the `new` operator, but unfortunately to Proxy the Set we have to call `new Proxy` and then either instantiate a Set in the Constructor or instantiate a Set and pass that in. Wouldn’t it be great if we could take over the constructor of your Set and have it return our new Proxied Set?

Get ready to go another level deep of inception, we can create a Proxy that will construct our proxy. Just like we wrote a function to trap the regular processes for getting property values from an object, we can also trap the construction of an object, like this:

```javascript
const MySet = new Proxy(Set, {
  construct: (target, args) => new Proxy(new target(...args), handler),
});

const set = new MySet();
```

As you can see we are passing in the Set object to a different proxy, not instantiating it. This will now let us trap the constructor and use it to return our Proxy wrapped set. Once again you can [see an example of this in action at my code sandbox.](https://codesandbox.io/s/my-set-liwcj)

%[https://codesandbox.io/s/my-set-liwcj]

Proxy objects are a great addition to the ECMAScript Spec. Now we can safely extend, add, or change the API of objects, including core JavaScript Objects. This can give us some amazing capabilities as we look to write safer and more composable JavaScript code in the future.
