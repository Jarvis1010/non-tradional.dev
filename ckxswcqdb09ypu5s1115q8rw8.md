---
title: "How to Avoid Prop Drilling with Composition"
datePublished: Thu Dec 06 2018 16:51:11 GMT+0000 (Coordinated Universal Time)
cuid: ckxswcqdb09ypu5s1115q8rw8
slug: how-to-avoid-prop-drilling-with-composition-6862cd4e253a
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316490307/GJvQmmKTy.jpeg
tags: programming, javascript, reactjs

---

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

React.js is one of the most popular front-end framework. It’s a great component based framework with a declarative API that allows the developer to think of the UI as a function of state and props and not as a function of time. In other words, you get to describe the appearance of your Component based on the data that is passed in.

The primary way data is passed around is by `props`. Simply put, `props` are the arguments to the functions that return JSX. In fact, the simplest form of a component is just that: a JavaScript function.

```javascript
const Header = (props) => {
  return (
    <div className={props.dark ? "dark" : "light"}>
      <h1>{props.title}</h1>
    </div>
  );
};
```

Props can be used for multiple things in the same Component. In the above example, we are using it to configure a css class: dark or light themed. It is also being used to provide the data of the header tag. We can then use the component by passing the props as attributes in our JSX, like this:

```javascript
const App = (props) => {
  return (
    <div>
      <Header dark title={"Title"} />
    </div>
  );
};
```

As you can see, `props` can be very powerful. However, if they are implemented in the wrong way, they can be very difficult to deal with. One of the biggest “No No’s” with `props` is do what is called “prop drilling.” Prop drilling is when a prop is passed to a Component only for the sole purpose of passing it down to a child.

```javascript
const Parent = ({ className, title }) => {
  return (
    <div className={className}>
      {/*...*/}
      <Child title={title} />
    </div>
  );
};
```

In the above example, the `title` prop is passed to the parent, but the parent doesn’t use it other than to hand it off to the child component. This is not an ideal way to pass data. Let’s say our child component has another prop that we need to take advantage of, we now have to pass that prop into the parent component only to then pass it to the child. As more time passes and our code base grows, the more error prone our data and components will be. (BTW, in the above example I am taking advantage of an ES6 operator called destructuring, which you can learn more about at [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment))

Despite the fact that we know to avoid it, prop drilling creeps in quietly with out us even noticing. Here is a real life example from a project I was working on. I was working on a Hero section that had a title, subtitle, and a list of ‘Call To Action’ buttons. This what I started out with (I simplified it for the example to make it easier to read):

```javascript
const Hero = ({ title, subtitle, ctas }) => {
  return (
    <LayoutWrapper>
      <Title>{title}</Title>
      <Subtitle>{subtitle}</Subtitle>
      <ButtonRow>
        {ctas.map((cta) => (
          <LinkButton {...cta} />
        ))}
      </ButtonRow>
    </LayoutWrapper>
  );
};
```

The above example took in three props: title, subtitle, and an array of cta(call to actions). The title and subtitle are put in their corresponding components and then we map over the ctas to get an array of LinkButtons. This is pretty simple to follow and it was working great.

Later on, I came across the need to have a row of LinkButtons somewhere else in my app. Luckily, React makes is easy to reuse code and I refactored out the button row from my Hero component so I could reuse it in both places. This is what I ended up with:

```javascript
const ActionButtons = ({ actions }) => {
  return (
    <ButtonRow>
      {actions.map((action) => (
        <LinkButton {...action} />
      ))}
    </ButtonRow>
  );
};

const Hero = ({ title, subtitle, ctas }) => {
  return (
    <LayoutWrapper>
      <Title>{title}</Title>
      <Subtitle>{subtitle}</Subtitle>
      <ActionButtons actions={ctas} />
    </LayoutWrapper>
  );
};
```

Did you see what happened? I didn’t even notice it at first. It was so easy to refactor out just the part I needed that I didn’t even notice that I had just created a prop drilling situation for myself. I was passing a ctas prop into the Hero component only to turn around and pass it on to another component. This could have cause some major headaches for me later on and all I was trying to do was take advantage of code reuse.

Luckily I caught myself and refactored the code this way:

```javascript
const Hero = ({ title, subtitle, children }) => {
  return (
    <LayoutWrapper>
      <Title>{title}</Title>
      <Subtitle>{subtitle}</Subtitle>
      {children}
    </LayoutWrapper>
  );
};

<Hero title={title} subtitle={subtitle}>
  <ActionButtons actions={actions} />
</Hero>;
```

As you can see, I am no longer explicitly rendering the ActionButtons component in my Hero component. I am simply rendering the children prop, which is available to every React component. This pattern puts the control back into the developers hand to choses what and if to render something there. I then pass the ActionButtons component as a child to the Hero component. This lets me pass in the data directly to the ActionButtons component without the Hero component even needing to know anything about the API of the ActionButtons component.

The pattern of passing in components as children is called “Composition”. It’s a powerful pattern that allows for both decoupling of components and code reuse. If you want to learn more about composition and how to use it, I would recommend you check out React’s getting started page on [Composition vs Inheritance](https://reactjs.org/docs/composition-vs-inheritance.html).

The advantage that my code now has is that as the ActionButtons component improves and changes, the Hero Component doesn’t need to know. It also gave the Hero Component more flexibility since it was no longer coupled to the ActionButtons Component so now it can render what ever I may need there or nothing at all. My code will be much more maintainable as my code base grows.
