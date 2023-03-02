---
title: "My React Context Best Practices"
datePublished: Thu Oct 22 2020 14:01:16 GMT+0000 (Coordinated Universal Time)
cuid: ckwx8o1z40cfqpqs18kwd7tmr
slug: my-react-context-best-practices-2e9288628ae7
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316757955/VSJ8YzWvu.jpeg
tags: javascript, web-development, reactjs, reacthooks

---

Photo by [mohammad takhsh](https://unsplash.com/@mohammadtkh?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the most common problems to solve in a React app is how to share state across multiple components. In React, one should only “lift” state up to the closest common parent in the component tree. From there, you typically share state by either passing props down, or by lifting components out via composition, or some combination of both, like this:

```javascript
const App = ({ user }) => {
  const user = useUserResource();

  return (
    <Layout>
      <NavBar avatar={user.avatar} userId={user.id} />
      <Main id={user.id} />
      <SideBar>
        <Title>{user.name}</Title>
        <Description>{user.description}</Description>
      </SideBar>
      <Footer />
    </Layout>
  );
};
```

Most of the time, this pattern is sufficient. But sometimes your components need to be stateful while sharing that state with other components at various levels in the component tree. What we need in those situations is to have some type of state “Provider” higher up in the tree that can broadcast state down to any “Consumer” that wants to listen to that state. Allowing our components to be coupled to that state and share it at the same time.

Older versions of React had an experimental context API that would solve this problem, but it was considered unstable for production use. Due to that, many people turned to 3rd party solutions, typically choosing Redux. Redux, if you are not familiar with it, is a general-purpose state management library based on both the flux architecture and highly influenced by the ELM architecture.

In the React implementation, one could couple any component to the Redux store allowing it to share state globally to any component in the component tree. However, Redux is a tool for global state management and was used to solve much more complicated state management problems than simply sharing state across components and it made many simple apps very complicated and cumbersome with little benefit.

Luckily, React 16 has brought many new features that have made it easier to write our applications. The new context API was added in v16.3, has finally given production stable API that solves the above problem without needing to bring in a 3rd party state management library.

### Context Basics

The context API is very simple. The first thing you need to do is create a context. You do this by calling `createContext` from the global React object, like this:

```javascript
const ColorCtx = React.createContext();
```

You can also just as easily destructure it from the import like this:

```javascript
import { createContext } from "react";

const ColorCtx = createContext();
```

This context now gives you two important components: the `Provider` and the `Consumer.` The `Provider` component is used by wrapping it around the part of the tree you want to broadcast down into and takes a single `value` prop, which is the value that is broadcast down, like this:

```javascript
const App = () => {
  return (
    <ColorCtx.Provider value={"blue"}>
      <MyApp />
    </ColorCtx.Provider>
  );
};
```

The `value` prop can be any value including arrays and objects, but for this example, we are using a simple string.

So we are broadcasting our color value with our ColorCtx.Provider, but that is useless unless we know how to listen to that broadcast. Up until React Hooks, there was only one way to listen to the value, and that was with the `Consumer` component on the context object. Since React 16.8 we now have access to the `useContext` hook as well.

The `Consumer` component uses the render prop pattern. It takes a function as it’s child. This function will be passed the value from the `value` prop in the provider and allowing you to return the JSX that uses that value, like this:

```javascript
const ContextButton = (props) => {
  return (
    <ColorCtx.Consumer>
      {(color) => {
        return <button style={{ color }} {...props} />;
      }}
    </ColorCtx.Consumer>
  );
};
```

In the above example, the color of the button will be provided via the `Consumer`Component, but the rest of the props are sent in as normal. This worked great, but it was a bit clunky, and some find the render prop pattern awkward when you pass a function as a child.

This was improved significantly with the `useContext` hook that was introduced with the hooks API. The `useContext` hook takes the entire context and returns the value. This means we can rewrite the above like this:

```javascript
const ContextButton = (props) => {
  const color = useContext(ColorCtx);

  return <button style={{ color }} {...props} />;
};
```

Now that you know the basics, here are some best practices that I follow when using the context API

### Only Lift Provider to a Common Ancestor

Because Redux why so widely used to solve this problem, we sometimes fall into the same mistake that the Context Api needs to be global and store everything in a single global app state. Context Providers can and should only live as high as they need to, in order to provide state to the components below.

### You Can Reuse The Same Provider

You can use the Same provider in different parts of your app. Nothing says that the context provider can only be used once. In fact, you can nest the same context provider in the component tree of the same Provider, like this:

```javascript
const App = () => {
  return (
    <ColorCtx.Provider value={lightTheme}>
      <Main />
      <ColorCtx.Provider value={darkTheme}>
        <SideBar />
      </ColorCtx.Provider>
      <Footer />
    </ColorCtx.Provider>
  );
};
```

In the above example, the sidebar will have a darkTheme while everything else will be a light theme. The consumer or useContext hook simply looks for the closest Provider of the same context in the component tree.

### Context Values Can Be Stateful

So far, we have been using providers with static values, and this is a perfectly legitimate way to use them. However, it is also very common to pair Context providers with state, such as the `useState` hook. You can pass both the state and functions that update state into the same provider value like this:

```javascript
const App = () => {
  const [theme, setTheme] = useState("light");

  const themeValue = {
    theme,
    toggleTheme: () =>
      setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light")),
  };

  return (
    <ColorCtx.Provider value={themeValue}>
      <Main />
      <SideBar />
      <Footer />
    </ColorCtx.Provider>
  );
};
```

As you can see from the above example, it is typically best to not pass the setState hook directly. Intstead, one should pass functions that do specific actions to update state. In the above example, we made a function to toggle between the light and the dark theme.This keeps the business logic close and easier to maintain.

### Don’t Expose the Raw Context

Finally, you could avoid exposing the Raw Context. For Providers, one should create customer Provider wrappers that encapsulates the logic of creating the provider. In addition to that, it lets you give a a more meaningful name than `value`to the prop that you will use to collect the data to be used for the context Provider, like this:

```javascript
function ColorProvider({ theme, children }) {
  return <ColorCtx.Provider value={theme}>{children}</ColorCtx.Provider>;
}
```

The same goes for the consumer. Yes you could use the `useContext` hook directly in your component, but it is much easier if you create a custom hook that does all that under the hood, like this:

```javascript
function useColorState() {
  return React.useContext(ColorCtx);
}
```

By creating these helper functions and components, we create a better developer experience while also allowing us to have the freedom to refactor how the underlying parts work.

Like I have said in previous posts, these are MY best practices that I push to follow. That said, I will “ignore” them any time I feel they make my code harder to read and maintain. Hopefully, they will help you as you use context in your React App.
