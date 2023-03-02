---
title: "Creating a Stateful Ref Object in React"
datePublished: Tue Apr 28 2020 16:55:13 GMT+0000 (Coordinated Universal Time)
cuid: ckwok9n8c0fp72ds1huza6ne0
slug: creating-a-stateful-ref-object-in-react-fcd56d9dea58
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316781620/vl2ZvJG-E.jpeg
tags: javascript, web-development, reactjs

---

Photo by [Jake Lorefice](https://unsplash.com/@jakelorefice?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

I have regularly [written](https://medium.com/the-non-traditional-developer/how-to-use-the-forwarded-ref-in-react-1fb108f4e6af) about how far `refs` have come in React. This is especially true using the `ref objects` What are ref objects exactly? Ref Objects are not in and of themselves special, they are simply just an object with a mutable `current` property. We could naively make our own ref like this:

```jsx
const Component = () => {
  const ref = {
    current: null,
  };

  //do something with ref.current
  return <div ref={ref} />;
};
```

The obvious problem with this is that on subsequent renders, the ref will get recreated. This is where the `useRef` hook comes in. It returns the same ref object on every render, which is more useful in our components. So now we can rewrite the above component like this:

```jsx
const Component = () => {
  const ref = useRef(null);

  //do something with ref.current
  return <div ref={ref} />;
};
```

Ref objects can be used for more than just DOM references. Any instance values that you need to keep across renders [can be saved in a ref object](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables) and changed by mutating the `current` property directly.

Despite this, ref objects have one interesting quirk: [they are not stateful](https://reactjs.org/docs/hooks-reference.html#useref). One can mutate the ref object all day long, but React won’t ever rerender just by simply changing the current property on a ref object.

To trigger a rerender one can use `useState` and update the state value directly by passing the returned set state function as a callback to the ref object, like this:

```jsx
const Component = () => {
  const [node, setNode] = useState(null);

  //do something with node
  return <div ref={setNode} />;
};
```

Though this works, there are two shortcomings to this process. One is that useState and ref objects work on a different lifecycle and therefore we now have to do null checks on the `node` value before we do anything with it. The other is that we have now lost the benefit of working with a consistent ref object throughout our app. Let’s say we start out with a ref object, then, later on, we need the ref to be stateful. We would then be required to refactor the component.

An ideal would be to have a hook that returns a ref object, whose current property will trigger a rerender when it is mutated, like this:

```jsx
const Component = () => {
  const ref = useStatefulRef(null);

  //updates to ref.current will trigger a rerender
  return <div ref={ref} />;
};
```

To do this we need to take advantage of an often overlooked feature of ES5 called getters and setters. Getters and Setters allow you to create functions that are called when users of our object read or mutate the value of a given property. Getters and setters have existed for a while now in other programming languages. In fact, many languages, such as C#, have now created shorthand syntactical sugar to make it easier to create such properties.

MDN has two great articles on both [getters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) and [setters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set). For sake of example, here is a quick arbitrary use of getters and setters:

```jsx
function createDog() {
  let sound = "bark";

  return {
    get call() {
      return sound;
    },
    set call(newCall) {
      if (typeof newCall === "string") {
        sound = newCall;
      }
    },
  };
}
```

which can be used like this:

```jsx
let fido = createDog();

fido.sound; // undefined

fido.call; //'bark'
fido.call = "meow!";
fido.call; //'meow!'

fido.call = 32;
fido.call; //'meow'
```

As you can see, even though we are accessing the call property as if it was a single value variable, under the hood JavaScript is calling the `get` function when it reads the value and the `set` function when it mutates that value.

As you can also see, these functions are not using an instance value on an object, but instead are changing a private value in a closure while also providing checks to ensure that when setting a new value, it must be a string to change it. You are also not required to have both a getter and a setter. If you wanted a property to be read-only, for example, you can provide only a get and it will allow you to read the value, but not change it.

How can we use this to make a stateful ref? Earlier, I mentioned that ref objects are not something special, they are simply an object that has a `current` property. So instead of using the ref object, what if we mimicked a ref object’s current property using getters and setters. This would allow us to use React’s `useState` under the hood to trigger a re-render while maintaining a consistent object shape.

Here is how we could do it:

```jsx
function useStatefulRef(initialVal = null) {
  let [currentVal, setCurrentVal] = React.useState(initialVal);

  const [statefulRef] = React.useState({
    get current() {},
    set current(value) {},
  });

  Object.defineProperty(statefulRef, "current", {
    get: () => currentVal,
    set: (newValue) => {
      if (!Object.is(cur, value)) {
        currentVal = newValue;
        setCurrentVal(value);
      }
    },
  });

  return statefulRef;
}
```

Let’s unpack all of that. First of all, we are using a `useState` hook to initialize the value and to get our setState function, which will be used to trigger the rerender.

Next, we are initializing our ref with our new imitation ref object that is using getters and setters for the `current` property instead of an instance property. You will notice two things about that initialized object, first is that we are adding the getters and setters, but they are not doing anything. That is ok, we will be redefining them with the real functionality later on in the code. The second thing is that we are using `useState` but only pulling off the initial value. This is purely for convenience and to make the code easier to read. You could also use a `useRef` hook, but then out object would be living inside another ref Object and there is just too much inception going on there.

Finally, before we return our statefulRef from our custom hook, we call the `Object.defineProperty` static method. This allows us to redefine our getters and setters to ensure we are using the most current values.

Our getter function is pretty straight forward. It just returns the currentValue when the `current` property is read. The setter, however, is doing something very interesting. First, we, are checking if the newValue is the same as the currentValue. This is to prevent unnecessary re-renders. Then if they are not the same, we are doing two things: First, we are mutating the `currentValue` directly, and then second, we are calling the setState function from `useState` to update the value with React, therefore triggering a rerender.

One might ask, why are we doing that? Shouldn’t just calling the setState function to be enough? Do you remember how I mentioned earlier how ref objects and state values don’t have their own life cycle? This is primarily due to the fact that ref objects are synchronous and state values are asynchronous. This means we need to do synchronous work first, by updating the `currentValue` directly, and then trigger the asynchronous work by calling setState.

There you have it, we now have a ref Object that is stateful. You can pass it around and use it just like any other ref object, except that it will trigger a rerender when you mutate the `current` property with a new value. As I mentioned earlier, I am working on an open-source project called, [Bedrock Layout Primitives](https://bedrock-layout.dev/), and one of the custom hooks I built for the project was [this exact hook](https://github.com/Bedrock-Layouts/Bedrock/tree/master/packages/use-stateful-ref). Feel free to check it out, use it, and tell me what you think.
