---
title: "How to Use The Forwarded Ref in React"
datePublished: Tue Mar 31 2020 14:01:00 GMT+0000 (Coordinated Universal Time)
cuid: ckxswurx40031k2s1ca944g3h
slug: how-to-use-the-forwarded-ref-in-react-1fb108f4e6af
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316460994/ANhIZmrlY.jpeg
tags: javascript, web-development, reactjs

---

Photo by [Gaelle Marcel](https://unsplash.com/@gaellemarcel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[Dealing with refs](https://medium.com/the-non-traditional-developer/a-beginners-guide-to-dealing-with-refs-in-react-7dac6a355964) has evolved a lot since React’s beginnings. React has gone from using a `ref` property on the component class and passing a string to the ref property of an element, to using `ref` callbacks that receive the DOM reference as an argument, to `ref` objects that expose the DOM reference in the on the `current` property of the `ref` object. Use of the `ref` object has also evolved, created with `createRef` and now more commonly used with the `useRef` hook.

As a component author, exposing the ref of an underlying element has also evolved. Originally it had to be exposed using some arbitrary prop, though conventionally named `innerRef` , and mapping it to the ref of the underlying element. Though this worked, React recently added a better way to expose the `ref` of an underlying element. Using `React.forwardRef` we can pass a function component and now have the ref forwarded to the component as the second argument after the props argument. Then it is as simple as mapping that ref to the underlying element, like this:

```javascript
const Div = React.forwardRef((props, ref) => {
  return <div ref={ref} {...props} />;
});
```

And now we can use our component as if it was any other React element:

```javascript
const App = () => {
  const divRef = React.useRef();

  return <Div ref={divRef}>{/*...*/}</Div>;
};
```

It would seem that all of our problems have been solved, but there is one final problem that has yet to be solved: using the forwarded ref. Sometimes we don’t want to just expose the ref, but use that same ref inside our component. One could naively, assume that you could simply use that same ref, like this:

```javascript
const Div = React.forwardRef((props, ref) => {
  useTheNode(ref.current);
  return <div ref={ref} {...props} />;
});
```

Two assumptions make this technique impractical. The first problem should be obvious in that the ref, though exposed, might not be used and therefore might be null.

The second problem might not be as obvious. In our implementation, we are assuming a ref object is being passed in, `ref` objects are not the only thing that can be passed as to the `ref` property. One can still pass a`ref` callback, which means that we cannot simply use the `current` property on the ref because we don’t know if it is a `ref` object or a `ref` callback.

Ultimately, what we need is some sort of hook that we can pass in the forwarded `ref` to and return a consistent `ref` object that we can use predictably, like this:

```javascript
const Div = React.forwardRef((props, ref) => {
  const safeRef = useForwardedRef(ref);
  useTheNode(safeRef.current);
  return <div ref={safeRef} {...props} />;
});
```

The first thing we need to do in our new hook is to generate a `ref` object that we will return and use in our component:

```javascript
const useForwardedRef = (ref) => {
  const innerRef = useRef(null);

  return innerRef;
};
```

Now we need to synchronize the new `refs` by doing a side effect. Any time we need to do a side effect, we need to use the `useEffect` hook. We need to check for three conditions, is the ref null, is the ref a function, or is it an object. If it is null, we do nothing. If it is a function, we call the function by passing `innerRef.current` to the function. And finally, if it is a `ref` object we can just make assign `innerRef.current` to `ref.current.`

Here is what our hook looks like now:

```javascript
const useForwardedRef = (ref) => {
  const innerRef = useRef(null);

  useEffect(() => {
    if (!ref) return;
    if (typeof ref === "function") {
      ref(innerRef.current);
    } else {
      ref.current = innerRef.current;
    }
  });

  return innerRef;
};
```

Given that we want to this to synchronize any time anything changes, we won’t pass in a dependency array to the `useEffect` hook.

There you have it, a nice simple hook that will allow you to have a safe and predictable `ref` object while still exposing it via `forwardRef.` Recently, I have started a new Open Source Library that I am calling [Bedrock Layout Primitives](https://bedrock-layout.dev/). One of the modules, installable via npm, written for this library is exactly the above code. Feel free to [check out the docs for useForwardedRef](https://bedrock-layout.dev/src-components-use-forwarded-ref) and [check out the code at Github.](https://github.com/Bedrock-Layouts/Bedrock)
