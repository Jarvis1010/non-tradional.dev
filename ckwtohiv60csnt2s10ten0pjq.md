---
title: "Your React Components Are Doing Too Much"
datePublished: Thu Jan 24 2019 16:20:57 GMT+0000 (Coordinated Universal Time)
cuid: ckwtohiv60csnt2s10ten0pjq
slug: your-react-components-are-doing-too-much-16e65968f419
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316563325/UdzYNl-_1.jpeg
tags: javascript, reactjs

---

Photo by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Components are the building blocks of React. They are the legos that let you build your app, piece by piece. Components can be huge or small. Just Like legos, bigger components are more difficult to work with. The more your component is concerned about, the harder it is to use and maintain in your app.

Let’s take this ClickerCounter component for example:

```jsx
class ClickCounter extends React.Component {
  state = {
    count: 0,
  };
  handleClick = () => this.setState((state) => ({ count: state.count + 1 }));
  handleReset = () => this.setState({ count: 0 });
  render() {
    const { dark } = this.props;
    const theme = dark ? "dark" : "";
    return (
      <div className={`clicker-layout ${theme}`}>
        <span className="clickerCount" onClick={this.handleClick}>
          {this.state.count}
        </span>
        <button className="primary" onClick={this.handleReset}>
          Reset
        </button>
      </div>
    );
  }
}
```

It’s not a very complicated component. You click on the span and the count increments, click on the button and it resets. It also changes to a dark theme if the `dark` prop is passed. Despite the fact that it’s not a complicated component, it is doing too much. In Robert Martin’s book _Clean Code,_ he recommends that functions should do one thing, do it well, and do it only. Our component is not following this principle. How do we fix it?

### Separate State Management

The easiest and most obvious concern that you can factor out is state management. The JSX in the render doesn’t technically care how the count is updated, all it really needs to care about is that it updates. Here is one way to do that:

```jsx
class ClickCounter extends React.Component {
  state = {
    count: 0,
  };
  updateCount = (val) => this.setState({ count: val });
  render() {
    const stateTools = [this.state.count, this.updateCount];

    return this.props.children(stateTools);
  }
}

const Clicker = (props) => {
  const { count, dark, onClick, onReset } = props;
  const theme = dark ? "dark" : "";
  return (
    <div className={`clicker-layout ${theme}`}>
      <span className="clickerCount" onClick={onClick}>
        {count}
      </span>
      <button className="primary" onClick={handleReset}>
        Reset
      </button>
    </div>
  );
};
```

Now they can be used together like this:

```jsx
const App = () => {
  return (
    <Counter>
      {([count, updateCount]) => {
        return (
          <Clicker
            count={count}
            onClick={() => updateCount(count + 1)}
            onReset={() => updateCount(0)}
          />
        );
      }}
    </Counter>
  );
};
```

The advantage of separating your state management into its own component is that now we can use whatever we want to manage state. In the future, we can switch to using React Hooks, Redux, React.Context, Mobx, Graphql, or whatever. No matter what we change it to, we will never once have to update the Clicker component. (In case you are wondering, the Counter component is using the Render Props pattern, which you can learn more about in the [react docs](https://reactjs.org/docs/render-props.html)).

### Separate Styling

Our Clicker component is still doing too much. Styling is an important thing, but how an element is styled is just an implementation detail. The Clicker component doesn’t need to know if you are using inline styles, a global style sheet, CSS modules, styled components, or whatever new tool gets invented in the future. To do this, we can separate our styles like this:

```jsx
const PrimaryButton = (props) => <button {...props} className="primary" />;

const ClickerCount = (props) => <span {...props} className="clickerCount" />;

const ClickerLayout = ({ dark, ...props }) => {
  const theme = dark ? "dark" : "";

  return <div {...props} className={`clicker-layout ${theme}`} />;
};

const Clicker = (props) => {
  const { count, dark, onClick, onReset } = props;

  return (
    <ClickerLayout dark={dark}>
      <ClickerCount onClick={onClick}>{count}</ClickerCount>
      <PrimaryButton onClick={handleReset}>Reset</PrimaryButton>
    </ClickerLayout>
  );
};
```

This separation of styles now allows us to create “styled” components that can be shared across our app. I can bring in pre-styled headers or nav items without needing to know how that styling was achieved.

### TOO MANY PROPS!!!!

The Clicker component still has problems. It has too many props. Later on, there might be a requirement to add another button that decrements the counter or the layout might need to change conditionally. You will also notice that many of the props on the Clicker component are just pass through props (i.e. prop drilling). The Clicker component is exposing too many implementation details.

How do we solve this? Inversion of control. Inversion of control, in a nutshell, basically means that you, as the component author, give control back to the user of your component to implement and customize it how they see fit. This can be done this way:

```jsx
const PrimaryButton = (props) => <button {...props} className="primary" />;

const ClickerCount = (props) => <span {...props} className="clickerCount" />;

const ClickerLayout = ({ className = "", ...props }) => {
  return <div {...props} className={`clicker-layout ${className}`} />;
};

const ClickerLayoutDark = (props) => {
  return <div className="dark" {...props} />;
};

const App = () => {
  return (
    <Counter>
      {([count, updateCount]) => {
        return (
          <ClickerLayoutDark>
            <ClickerCount onClick={() => updateCount(count + 1)}>
              {count}
            </ClickerCount>
            <PrimaryButton onClick={() => updateCount(0)}>Reset</PrimaryButton>
          </ClickerLayoutDark>
        );
      }}
    </Counter>
  );
};
```

As you can see in the example above, I changed the ClickerLayout to accept a `className` prop. I can then make two versions of the layout: the default and the dark themed one. This now gives the control back to the user on which layout to use. I have also got rid of the Clicker Component entirely. Now we no longer have to pass all kinds of props around. Just compose it how you want.

I heard a quote once that said, “…to write clean code doesn’t mean you write less code.” The original ClickerCounter component is actually fewer lines of code, but it was doing too much and as it grew it would have been much more difficult to manage. Separating out all the concerns into individual components can be more code, but it is cleaner code which allows for more flexibility in how you work. Your lego pieces are smaller and much easier to manage.
