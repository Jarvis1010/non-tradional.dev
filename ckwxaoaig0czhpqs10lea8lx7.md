---
title: "The Several States of the`useState` Hook in React"
datePublished: Thu Sep 17 2020 20:15:56 GMT+0000 (Coordinated Universal Time)
cuid: ckwxaoaig0czhpqs10lea8lx7
slug: the-several-states-of-the-usestate-hook-in-react-113b346cb63c
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316723568/vd5W9etMp.jpeg
tags: javascript, reactjs

---

Photo by [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Hooks have changed the way we write React components. It encourages a much more functional and composable style of coding that was difficult to achieve with class components. One of the biggest game-changers is the `useState` hook.

Before hooks, React state was managed by a single `state` property on the class component. This property had to be an object and had to be updated with a class method called `this.setState` which despite its name, didn’t set the state, it patched the state with whichever object we provided it. Though this worked well, it encouraged us to throw all the state in this one object, even though the value were not related or updated together. It wouldn’t be uncommon to have a state object look like this:

```javascript
const state = {
  toolTipIsVisible: false,
  userList: [
    /* ... */
  ],
  formData: {
    /* ... */
  },
  matchesMedia: false,
  inViewPort: true,
  /* ... */
};
```

All this state was data that need to be saved and maintained, but they didn’t need to be maintained together nor live in the same object, other than this was the limitation of the class component. This also made refactoring difficult, because it wasn’t always obvious which parts of the state needed to stay together and could be lifted into a parent component or dropped into a child component.

In an attempt to clean some of this up, Higher-Order Components and Render Prop patterns emerged. Though this did allow the state and functions that maintained them to be grouped, it was still just a hack and we needed a proper primitive to handle this better. This is where Hooks come in.

### Basic Use Cases

The `useState` hook is very simple. You can either invoke it on the `React` object directly or destructure it from the import like this:

import React, {useState} from 'react'

The `useState` hook simply takes an initial value and will return an array with two values in it. The first value is the current value and the second value is the method you need to update the value. Here is an example:

```javascript
const stateArray = useState(/* initial value */);

const value = stateArray[0];
const setValue = stateArray[1];
```

Luckily, using [array destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment), this can be simplified to one line like this:

```javascript
const [value, setValue] = useState(/* initial value */);
```

So now we can rewrite our component state like this:

```javascript
const [toolTipIsVisible, set toolTipIsVisible] = useState(false)

const [userList, setUserList] = useState([/* ... */])

const [formData, setFormData] = useState({/* ... */})

const [matchesMedia, setMatchesMedia] = useState(false)

const [inViewPort, setViewPort] = useState(false)}
```

(From the above, you will notice a convention that has evolved naturally since hooks were first introduced. That pattern is not required but is typical in many codebases now. It is `const [<name>, set<Name>] = useState().` )

One might ask how this is better, in some ways you now have to keep track of more methods to update the individual states. But the nice benefit this gives us is flexibility. For example, the `matchesMedia` and `inViewPort` probably could be refactored into their hook, depending on how your components are built, you may be able to move the `userList` and `formData` down into a child component. We can refactor it to be something like this:

```javascript
const Form = () => {
  const [formData, setFormData] = useState({
    /* ... */
  });
  /* ... */
};

const Users = () => {
  const [userList, setUserList] = useState([
    /* ... */
  ]);
  /* ... */
};

const App = () => {
  const [toolTipIsVisible, setToolTipIsVisible] = useState(false);
  const matchesMedia = useMatchMedia(/* ... */);
  const inViewPort = useIntersection(/* ... */);

  /* render the Form and Users and everything else */
};
```

When each state is managed separately, it’s as simple as cut & paste and you have done most of the refactoring right there.

### Deriving State From the Previous State

The setState function that is returned from `useState` can be used in two ways. The easiest and simplest way is to just pass the new value into it, like so:

```javascript
const [isVisible, setIsVisible] = useState(true);
/* ... */

<button onClick={() => setIsVisible(false)}>Close</button>;
```

This function, however, can also accept a function. This function takes the previous state as an argument and then whatever is returned from the function will be set as the new state. Like this:

```javascript
const [isVisible, setIsVisible] = useState(true);
/* ... */

<button onClick={() => setIsVisible((prevState) => !prevState)}>
  Toggle SideBar
</button>;
```

One might ask, why would you need to do this? Couldn’t you just as simply derive the state from the current value like this:

```javascript
setIsVisible(!isVisible);
```

There is one big reason that this can be problematic: concurrency. When React renders, it has to live in a world where asynchronous processes are the norm. The setState function return from the useState hook is asynchronous already and then if you add on top of that the code you write that calls the function is quite possibly asynchronous as well you have no way to guarantee what has happened to the state while you wait for the code to update. This means that the value of your state can become stale by the time your function runs.

You could write code like the example above without using a function and never have a bug, but I would highly recommend that if the new state is derived from the previous state, that you use the function pattern to ensure that your updates are consistent

### Objects Can Be State Too

The `useState` hook has removed the requirement to keep the state in a single object, but that doesn’t mean we stop using objects as state values. Some values need to be group together, which is why we have objects in the language in the first place.

The confusion arises when we have values that need to be grouped but updated independently, like form state for example. It might be tempting to break up the object into separate `useState` hooks to make updating the values easier, but this is problematic for two reasons. First is that we lose the benefit and semantics of having an object. Secondly, we can already update individual values with the current `setState` functions. All we have to do is take advantage of the spread operator like this:

```javascript
setFormData((prevForm) => ({ ...prevForm, userName: "newUserName" }));
```

[Spreading object values](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) into a new object has been around since 2015 and allows you to do a shallow copy of all the values of one object into a new object. We can then take advantage of how JavaScript Objects works, namely that you cannot have duplicate keys and if you set the key more than once, the last one wins. Just like above, since we are deriving that state from a previous state, we will use the function syntax.

The `useState` hook, though simple, is a very versatile hook. By using it to its full potential, you can do many, if not all the state management needs your app needs.
