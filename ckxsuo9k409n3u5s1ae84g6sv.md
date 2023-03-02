---
title: "Coding a Finite-State Machine in a React Hook"
datePublished: Thu Mar 07 2019 19:54:22 GMT+0000 (Coordinated Universal Time)
cuid: ckxsuo9k409n3u5s1ae84g6sv
slug: coding-a-finite-state-machine-in-a-react-hook-9c58e2296df0
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316528125/js4sDNvsm.jpeg
tags: javascript, reactjs, reacthooks

---

Photo by [Harshal Desai](https://unsplash.com/@harshaldesai?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

The biggest buzz in the React Community right now has got to be ‘Hooks.’ Hooks is a new API announced at ReactConf 2018 and was just released as stable in react 16.8.0. Hooks, simply put, allows you to solve many of the same problems you used class components for, but in a composable and functional way. At the same time, there is a not so new method for modeling state that has existed long before react was even thought about. This method is known by many names but often is called a finite-state machine or even just a state machine.

In this post, I thought it would be interesting to implement an old data modeling process with the new hooks API. Specifically, I will be implementing a common pattern in front-end development: asynchronous fetching of data.

### What is a Finite State Machine

I am not going to get into the weeds and talk about the nuances and theory of finite state machines. There are many [resources](https://en.wikipedia.org/wiki/Finite-state_machine) that do that already. In practical terms, a finite state machine is a thing, for lack of a better word, that can be in exactly one of a limited number of ‘states.’

A state machine can transition from one state to another, but it cannot transition to a state that isn’t already predetermined. ‘State’ can be anything including what the next state or states are that can be transitioned to, as well as the conditions needed to move to the next state.

An example of a finite state machine that most of us interact with on a regular basis is a traffic light. A typical traffic light can is in one of three states: the “go” state, the “stop” state, and the “get ready to stop” state. If we were to model this in JavaScript it might look like this:

```javascript
const GO_STATE = {
  lightColor: "green",
};

const STOP_STATE = {
  lightColor: "red",
};

const ABOUT_TO_STOP_STATE = {
  lightColor: "yellow",
};
```

Traffic lights also don’t just bounce around from one state to another haphazardly. They move from green to yellow to red and then it starts over at green again. We could model that like this:

```javascript
const GO_STATE = {
  lightColor: "green",
  nextState: "aboutToStop",
};

const STOP_STATE = {
  lightColor: "red",
  nextState: "goState",
};

const ABOUT_TO_STOP_STATE = {
  lightColor: "yellow",
  nextState: "stopState",
};
```

We can continue to go into even more details, such as what triggers state changes, light duration, if the light is in a state of error and so on, but I think you get the point. A traffic light can be in only one of three predetermined states and therefore is considered a finite state machine.

How would we go about modeling our fetching state machine? First of all, when fetching, our component will be in one of three possible states: loading, success, or error. As far as how we transition from one state to another, the initial state will always be loading and then it will either be resolved into a successful state or it will be rejected into an error state.

We can represent all that in JavaScript like this:

```javascript
const defaultState = {
  error: false,
  success: false,
  loading: false,
  results: null,
};

const fetchStates = {
  loading: { ...defaultState, loading: true },
  success: { ...defaultState, success: true },
  error: { ...defaultState, error: true },
};

const doFetch = async (...args) => {
  setInitialAState(fetchStates.loading);
  try {
    const res = await fetch(...args);
    if (!res.ok) throw res;

    const results = await res.json();
    updateState({ ...fetchState.success, results });
  } catch (err) {
    updateState(fetchStates.error);
  }
};
```

As you can see we have defined all 3 states in the fetchStates object. When we run the doFetch function, we initialize the state to the loading state, we fetch the data, and if the fetch is successful we update the state to the success state with the results. If there was an error, on the other hand, we update the state to the error state.

### Hooks

So we have started implementing our state machine, but how do we do that with Hooks? First of all, if you haven’t learned about hooks and the motivation behind them, check out the [intro to hooks](https://reactjs.org/docs/hooks-intro.html) in the React docs. What we are going to do is try to make our own custom hook by composing a couple of core hooks built into React.

The obvious first hook we should look at is the `useState` hook. The `useState` hook is a function that takes an initial state value and returns an array with the current state value as the first value in the array and a function used to update that state as the second value. Here is a simple example of how this is used:

```javascript
const [currentState, updateState] = useState(0);

console.log(currentState); //0

updateState(2);

console.log(currentState); //2

updateState("banana");

console.log(currentState); //"banana"
```

As you can see, the value passed to the useState hook is the initial value of currentState. Every time we call updateState, it will update the value of currentState to whatever we pass in, even if we change the type. (Technically hooks will only work properly inside the context of a component, the above example is for illustrative purposes only).

The other hook we will need in order to build our custom hook is the `useEffect` hook. The `useEffect` is an escape hatch that allows one to do imperative work in an otherwise declarative model. The hook takes a callback that will be called when the component ‘mounts’ and then called again every time the component is updated and rerendered.

If you want to control if a callback is run on rerenders, then you can provide an array of values that will be checked. If the values don’t change, then the callback is not rerun. If your component needs to do cleanup when it unmounts, then you can provide a function that does the cleanup as the return value of the callback.

Here is a simple example of useEffect stolen straight from the official docs:

```javascript
useEffect(() => {
  document.title = `You clicked ${count} times`;

  return () => (document.title = defaultTitle);
}, [count]);
```

This hook will update the title of the page when it’s component mounts and will only update the title on future rerenders if the value of `count` changes. When the component unmounts it will run the function returned from the callback, which restores the title to the defaultTitle.

### Building Our First Custom Hook

We have our modeled states and now using our two hooks, `useState` and `useEffect` , we are ready to finally build our custom fetch hook. Let’s start with the finished product:

```javascript
const defaultState = {
  error: false,
  success: false,
  loading: false,
  results: null,
};

const fetchStates = {
  loading: { ...defaultState, loading: true },
  success: { ...defaultState, success: true },
  error: { ...defaultState, error: true },
};

const useFetch = (url) => {
  const [fetchState, setFetchState] = useState(fetchStates.loading);

  useEffect(() => {
    (async () => {
      setFetchState(fetchStates.loading);
      try {
        const res = await fetch(url);
        if (!res.ok) throw res;

        const results = await res.json();
        updateState({ ...fetchState.success, results });
      } catch (err) {
        updateState(fetchStates.error);
      }
    })();
  }, [url]);

  return fetchState;
};
```

As you can see, we are passing the loading state as the initial state to the `useState` Hook and then setting again at the beginning of our `useEffect`. This ensures that when ever the effect is run that is will reset it back to the loading state.

Next, we have moved the inside of our `doFetch` function into the `useEffect` hook. The callback that is passed into the `useEffect` hook cannot be asynchronous nor can it return a promise. In order to use the `async/await` keywords, we are using the an IIFE pattern. And IIFE, or immedately invoked function expression, declares a function inside two parathese and then it is called right away with another set of paragraphs, like this:

```javascript
(() => {
  console.log("This function is called right away.");
})();
```

Using this pattern lets us declare and async function and then call it right away. If you don't like IIFEs then you could just assign it to a variable and then call it right after like this:

```javascript
useEffect(() => {
  const doFetch = async () => {
    setFetchState(fetchStates.loading);
    try {
      const res = await fetch(url);
      if (!res.ok) throw res;

      const results = await res.json();
      updateState({ ...fetchState.success, results });
    } catch (err) {
      updateState(fetchStates.error);
    }
  };

  doFetch();
}, [url]);
```

Since our effect has nothing to clean up, we are not returning a cleanup function from the `useEffect` callback. Since our Effect is dependent on the url being passed in, we are passing that to the dependency array, so that if the url ever changes, it will rerun our effect hook. Finally, we are returning `fetchState` from our useFetch hook.

Here is how you would use this custom hook in a React Component:

```javascript
const UserProfile = () => {
  const { loading, error, results } = useFetch("url.com");

  return loading ? (
    <Spinner />
  ) : error ? (
    <ErrorMessage />
  ) : (
    <DisplayUserDetails user={results} />
  );
};
```

When the `UserProfile` component mounts, the useFetch hook will return the loading state. This will let me show the `Spinner` component until the internal fetch resolves or rejects. If it rejects, then the error state will be returned and I can show the `ErrorMessage` component. If it resolves, then we can use the data found in the success state under the results property.

As we learn new things, it is important to remember that there are still tried and true patterns that we can rely on to help us solve problems. The new tools can help us implement them in a new way, but we don’t have to ‘re-invent the wheel’ every time a new tool is created to help us. Old patterns can still be used effectively in new ways to help us solve our day to day problems.
