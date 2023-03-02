---
title: "A Beginner’s Guide to Dealing With Refs in React"
datePublished: Thu Jan 17 2019 15:49:22 GMT+0000 (Coordinated Universal Time)
cuid: ckwzi5gy20cy9b6s1amt990be
slug: a-beginners-guide-to-dealing-with-refs-in-react-7dac6a355964
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316675236/MD-IIB6sj.jpeg
tags: javascript, reactjs

---

Photo by [Jake Lorefice](https://unsplash.com/@jakelorefice?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

React is currently one of the most popular JS frameworks. There are many reasons why this is, but it pretty safe to say that one of those reasons is the declarative way you build apps in React. React is built from components. Components are a description of the DOM. How that translates to the actual DOM is abstracted away from you so you can focus on the part that matters most: building your app.

This is very powerful, but sometimes you still need access to the actual DOM instance. Maybe you need to programmatically bring focus to an element or use a third party library that needs access to the actual DOM element. State and props can get you far, but it doesn’t give you access to the actual DOM element. Luckily, React provides an escape hatch so you can do just that. We do this using “Refs.”

There are technically three ways to create refs, but only two recommend ways. The first method is the deprecated string method. You pass in a string to `ref`in the JSX and the ref is saved in this.refs under that string like this:

```javascript
class UserForm extends React.Component {
  componentDidMount() {
    this.refs.usernameInput.focus();
  }

  render() {
    return (
      <form>
        Username: <input ref="usernameInput" />
        Password: <input />
      </form>
    );
  }
}
```

Once again I want to emphasize, this method is deprecated. It has bugs and is likely to be removed in a future version of React. **Do not use it.** I really am only providing it for sake of completion.

The next way to create refs is to use the callback method. Instead of passing a string, you pass a callback function. On render, it will pass the ref as an argument to the callback. From there you can do things with the ref including saving it off for later use. Let’s convert the above example to use the callback method:

```javascript
class UserForm extends React.Component {
  constructor(props) {
    super(props);

    this.handleRef = this.handleRef.bind(this);
  }

  usernameInput = null;

  componentDidMount() {
    this.usernameInput.focus();
  }

  handleRef(ref) {
    this.usernameInput = ref;
  }

  render() {
    return (
      <form>
        Username: <input ref={this.handleRef} />
        Password: <input />
      </form>
    );
  }
}
```

As you can see we are no longer using `this.ref` to access the `usernameInput` We are passing in a callback which lets us save it on a property in the class. From there we can access the node directly from the usernameInput property on the class itself.

It may be tempting to do the call back inline. This is not a good idea. If you want to get more information about why then you can check out [this page from React’s docs](https://reactjs.org/docs/refs-and-the-dom.html%5C) to see why.

The final way was added to React 16.3.0 . You can use`React.createRef()` and pass it that to a ref like this:

```javascript
class UserForm extends React.Component {
  constructor(props) {
    super(props);

    this.usernameInput = React.createRef();
  }

  componentDidMount() {
    this.usernameInput.current.focus();
  }

  render() {
    return (
      <form>
        Username: <input ref={this.usernameInput} />
        Password: <input />
      </form>
    );
  }
}
```

This method eliminates the need to initialize the property with null, create the callback and bind the callback to `this` . The other difference is that the reference is accessed from the `current` property where the ref was created.

### Refs and components

This is all good when we are dealing with DOM nodes in the JSX, but what about components? There are two big gotchas when dealing with components.

The first gotcha is that what is returned from the ref attribute of a component is dependent upon if the component is a class component or a functional component. If it is a class component, you get the ref of the object created by React, **NOT** the DOM Node. If the component is a functional component, then your app will get a reference error and the app will crash.

The second gotcha is that refs are not props. Let me repeat that, refs are not props. This means that you cannot get access to the ref from `this.props` in Class components nor can you get it from `props` in functional components. So as a component author, if you need to give access to refs how do you do it?

Prior to version 16.3.0 the only way to do this was to pass it onto a prop(not ref, ref is not a prop) and then map that to the appropriate ref in the component like this:

```javascript
const Article = (props) => (
  <article ref={props.innerRef}>{props.children}</article>
);
```

This pattern works fine, but it means that there is no standard way to refer to forward the ref. It’s just a prop and can be named anything. Each component author, therefore, can use whichever convention they choose to refer to the ref.

In React 16.3.0, `React.forwardRef()` was introduced. This was a wrapper that gives you access to the ref as the second parameter. This means we can rewrite the above example like this:

```javascript
const Article = React.forwardRef((props, ref) => (
  <article ref={ref}>{props.children}</article>
));
```

Though their use should be limited, knowing how to use refs is important. I am just scratching the surface. I would highly recommend that you read about [Refs](https://reactjs.org/docs/refs-and-the-dom.html) and [forwarding refs](https://reactjs.org/docs/forwarding-refs.html) in more detail in React’s documentation.
