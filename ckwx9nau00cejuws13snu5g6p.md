---
title: "The Newbie’s Guide to Using The useReducer Hook in React"
datePublished: Wed Dec 16 2020 15:33:25 GMT+0000 (Coordinated Universal Time)
cuid: ckwx9nau00cejuws13snu5g6p
slug: the-newbies-guide-to-using-the-usereducer-hook-in-react-585d605e23fc
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316736733/Q94sEy2rJ.jpeg
tags: javascript, reactjs

---

Photo by [Suzanne D. Williams](https://unsplash.com/@scw1217?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

When I started with React, React had one way to manage state: Class components. Any time you needed to lift or lower state in your React App, you often had to refactor function components into Class Components to make that possible. This could cause some very tricky refactoring because now you were passing not just state, but class methods that updated that state. These methods also had to be bound to the correct lexical scope, otherwise, our App would not run correctly.

This was why Redux was often brought in to React Apps. Redux, being a general-purpose global state library, had two major selling points. First, it provided a centralized state object, called a store, that could be subscribed to whenever changes took effect. Secondly, it was built off of the flux architecture, a functional style architecture that enforces a unidirectional flow of state management.

This made the decision on how to manage the app state easier. One simply lifted anything you wanted into the global store and then any part of your app could then subscribe to the store and update accordingly.

Luckily, for us, the React team gave us two composable hooks that let us manage state in our React applications. These hooks can much more easily be composed into any React Component or into their own custom hook. [We have already talked about the](https://medium.com/the-non-traditional-developer/the-several-states-of-the-usestate-hook-in-react-113b346cb63c) [useState](https://medium.com/the-non-traditional-developer/the-several-states-of-the-usestate-hook-in-react-113b346cb63c) [hook](https://medium.com/the-non-traditional-developer/the-several-states-of-the-usestate-hook-in-react-113b346cb63c), but there is another hook that lets us handle more complex state management. It’s called the `useReducer` hook.

### What is the useReducer Hook?

The useReducer hook is heavily inspired by Redux. I mentioned above that Redux had two major selling points: easy access to a central state store and predictable state management through the flux architecture and [functional-style programming](https://medium.com/the-non-traditional-developer/functional-programing-for-your-everyday-javascript-developer-f5efad54397d), namely an immutable state that is updated via unidirectional flow.

This was implemented via “dispatching actions” into the store and a reducer function would take those actions and derive the new state from the previous state and the action. The useReducer hook works that same way, it takes a reducer function and initial state and returns the current state and a ‘dispatch function’ that we can use to trigger state changes, like this:

```javascript
const App = () => {
  const [state, dispatch] = useReducer(reducerFunc, initialSate);

  /*
   * component implementation goes here
   */
};
```

### What is a Reducer function?

When looking up the documentation, a reducer is a function that takes a state and an action and returns a new state. For me, this was confusing. Why is that a “reducer function”? What was being reduced? Then someone showed me this and it has stuck with me forever.

One of the [holy trinity of array methods](https://medium.com/the-non-traditional-developer/map-filter-reduce-the-holy-trinity-of-array-methods-16ce3bdb69e2) is the reduce method. The reduce method takes a function and an initial value like this:

```javascript
const reduceFunction = (accumulator, currentValue) => newAccumulator;

arr.reduce(reduceFunction, initialValue);
```

This is simple enough to understand, for each item in the array, it will pass in the current value of the accumulator and the current value in the array. In the function, you will then return the new derived value of the accumulator based on the current value of the array.

That should sound very similar to the reducer function definition required for the useReducer hook. In fact, with a few tweaks, we have exactly that:

```javascript
const reducerFunction = (state, action) => newState;

useReducer(reduceFunction, initialValue);
```

The function definitions are the same. The only difference is one is used on an array of values and the other is used on a single previous state to produce a new state.

What about the action. What does it need to be? Technically you can pass any value to the dispatch function and it will be passed into the reducer function as the action. That said, many take a page out of the Redux library and pass an “action” object.

An action object is simply an object with a `type` property that describes that action, like this: `{ type: "update-user" }.` Then the reducer function can use that type to know how to derive the new state. Once again, this is a convention and not required by React at all.

[A fantastic example of the useReducer hook](https://reactjs.org/docs/hooks-reference.html#usereducer) in action can be found over at the react docs. I highly recommend you check it out.

### What Are The Advantages of useReducer vs useState

First of all, every blog post that compares the `useState` hook to the `useReducer` hook is required to point out that the useState hook is implemented under the hook with the useReducer hook. That said the `useReducer` hook has two advantages over the `useState` hook.

The first is that it allows for complex state orchestration. I use the word orchestration on purpose. The useState is very simple, and much of the time simple is all we need. That said when you find yourself updating more than one state at the same time, You should probably consider using the useReducer hook.

The second advantage is that the useReducer hook lets you co-locate all your state logic together and often lets you use the same wording in your business requirements, such as “update-user”, “logout”, “reset”, and so forth. Something as simple as “logging out” can require multiple states to need to be updated. This is much easier to manage if all the logic for updating the state is in one function.

I have a great example of both [over at a codesandbox](https://codesandbox.io/s/zelda-moving-boxes-hom9z?file=/src/App.tsx:2451-2458). I am using a reducer to control a sprite of Link walking around the page. Doing something as simple as moving up, down, left, or right can result in multiple things needing to happen at the same time. The useReducer makes that logic much easier to maintain.

Ultimately, the choice is up to you the developer. React now has first-class support for managing state in a simple or complex way. Both can easily be lifted or lowered into any part of our Component tree that we need. Resulting in a much easier App to manage.
