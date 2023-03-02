---
title: "An Intro to Solid.js for React Developers"
datePublished: Thu May 12 2022 20:43:23 GMT+0000 (Coordinated Universal Time)
cuid: cl33h9tym00yjsanv8zgw519n
slug: an-intro-to-solidjs-for-react-developers
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/CcO438ezU80/upload/v1652387962045/0BnSMjNm3.jpeg
tags: javascript, web-development, reactjs, solid, theme

---

In the 2021 state of JS survey result, solid.js, in its debut appearance, surprised many by jumping straight to the top of the satisfaction score. It beat out not just React but Svelte, the 2020 satisfaction leader. While React still maintains a significant market share, ignoring solid.js would be a mistake.

## Solid feels very familiar to react developers

Let's look at this arbitrary counter app written in solid.js:

```javascript
function Counter() {
  const [count, setCount] = createSignal(0);
  return (
    <div>
      <p>The current count is: {count()}</p>
      <button onClick={() => setCount((x) => x + 1)}>Plus</button>
    </div>
  );
}
```

I am confident that any React developer can understand everything in the above component, despite never seeing solid.js before. This familiarity is because solid.js has an API familiar to React Developers. For one thing, solid.js utilizes JSX, which means elements and components can be composed in many of the same familiar ways that React developers have built an intuition for. For example, we can make this arbitrary Welcome component using just our intuition from building React Apps:

```javascript
function Welcome(props) {
  return <p>Welcome, {props.name}</p>;
}
```

Solid.js also utilizes primitives with an API that is very similar to how one would use React hooks. The API of many of solid's reactive primitives are almost identical to their React Hook counterparts.

It's important to point out that solid.js is not attempting to follow React's API one-to-one. You cannot take a React app and start using it in solid.js. It does, however, have an API that is familiar to your average React developer, which makes the learning curve much nicer.

## It's all about the primitives

Despite its superficial similarities, what makes solid.js unique is the ways that it is not like React. One of those ways is that, unlike React, the component is not the foundation of solid.js apps. Solid.js proudly describes its components as "vanishing components." Components are helpful for code organization and cease to exist once the initial render occurs.

For solid.js, it's all about the primitives. Solid.js has a suite of "fine-grained reactive primitives." These primitives can feel very similar to React Hooks, but they are not hooks. In React, hooks are dependent upon the React component lifecycle and require you to have at least a rudimentary understanding of the Virtual DOM and the React render lifecycles. This dependency on the React lifecycle is why there are so many "rules of hooks" that you need to know to use them correctly. Let's take this React Component, for example:

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `The current count is: ${count}`;
  }, [count]);

  return (
    <div>
      <p>The current count is: {count}</p>
      <button onClick={() => setCount((x) => x + 1)}>Plus</button>
    </div>
  );
}
```

Despite how simple this component is, there are several rules you need to remember, and forgetting any of them can cause unwanted problems. Solid's reactive primitives, on the other hand, just work. They can be called conditionally, and they don't even need to be called in a component themselves. The above counter component can be rewritten in solid.js like this:

```javascript
const [count, setCount] = createSignal(0);

createEffect(() => {
  document.title = `The current count is: ${count()}`;
});

function Counter() {
  return (
    <div>
      <p>The current count is: {count()}</p>
      <button onClick={() => setCount((x) => x + 1)}>Plus</button>
    </div>
  );
}
```

We have moved all the reactive primitives outside of the component itself in the above component. We can do this because Solid's reactive primitives themselves, not the components, are what solid.js is built on and where the "magic" happens.

## Reactivity VS Virtual DOM

Like many popular frameworks, React is built on the concept of a "Virtual DOM." A virtual DOM is an in-memory representation of the actual DOM. The idea is that doing things in memory is less expensive than working with the actual DOM, so one can run and re-run components multiple times, making changes to this virtual DOM. Only if there are actual differences between the virtual DOM and the real DOM will React reconcile those changes from the virtual DOM to the actual DOM. You are literally hooking into the React Virtual DOM Lifecycle when you use hooks.

Solid.js, on the other hand, depends on "fine-grained" reactivity. When you write a JSX expression that relies on a reactive signal, for example, only the part of the JSX that depends on that signal's value is wrapped in a function that is re-run if that signal changes. The same goes for effects run in the `createEffect` primitive. If the effect has a dependency on one or more reactive values, it will be re-run only if the reactive value changes. There is no need for a dependency array since solid knows in advance if the value can be changed and will re-run only those effects that depend on the changed reactive values.

This is why solid components are "vanishing," and reactive primitives can be called outside of the component. After the initial render, only those fine-grained parts of the app will continue to exist.

## Gotchas coming from React

This difference in how the two frameworks work under the hood can lead to two of the biggest "gotchas" that React developers fall into when starting with solid.js. The first gotcha is you need to re-teach yourself that components don't ever get re-run. Those things that we have taught ourselves that we should (and shouldn't) do in a React component no longer apply in Solid.js. We don't need hooks like `useCallback` and `useRef`, because the component never re-runs. Instead, we can just assign them to variables like any other JavaScript code we write:

```javascript
function MyForm() {
  const handleSubmit = (e) => {
    /* handl submit */
  };

  let myInput;
  // use onMount or createEffect to read after connected to DOM
  onMount(() => myInput.focus());

  return (
    <form onSubmit={handleSubmit}>
      <input ref={myInput} />
    </form>
  );
}
```

The second is that you can break the fine-grained reactivity if you destructure the props. The props object may or may not have reactive values in them. By destructuring them, that reactivity can be lost. So instead of writing code like this:

```javascript
function Greeting(props) {
  const { greeting, name } = props;
  return (
    <h3>
      {greeting} {name}
    </h3>
  );
}
```

It would be best if you wrote it like this:

```javascript
function Greeting(props) {
  return (
    <h3>
      {props.greeting} {props.name}
    </h3>
  );
}
```

Or you can utilize solid's `mergeProps` or `splitProps` utility functions, which allow you to merge or split objects with reactive values without breaking reactivity.

When React was introduced, it challenged the best practices of the day. That challenge has had a lasting impact on how we write web applications. Solid is here to continue to push us in that same direction that React started.
