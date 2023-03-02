---
title: "Styling Best Practices using React"
datePublished: Thu Apr 04 2019 17:55:29 GMT+0000 (Coordinated Universal Time)
cuid: ckwojtf470fl22ds1h7ae5334
slug: styling-best-practices-using-react-c37b96b8be9c
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316788907/TmFQwmsXl.jpeg
tags: css, javascript, reactjs, styling, styled-components

---

Photo by [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the most enjoyable parts of working on the web is that you get to work in such a visual medium. I big part of the visual aspect is obviously the way it looks. Styling a web page has come along way from its early roots of adding markup and attributes to change things like fonts and colors, to Cascading Style Sheets of today. Now in our world of Web applications and JS Frameworks, the web has evolved and so have best practices. This is especially true when working in a framework like React.

### How Does One “Style” Your Web Page Using React?

React, on its own and without any other tool, support two ways to style elements: the `style` prop, the style tag, and CSS style sheets. The style prop takes a JS object of CSS properties and values and ultimately translates that to inlines styles of an element, like this:

```jsx
// JSX
<div style={{ color:'red'}}>
   Red Text
</div>

//DOM
<div style="color:red;">
   Red Text
</div>
```

Since it’s just inline styles, you still get the highest specificity but you are limited on what you have access to style.

The style tag and CSS style sheets work just the same as working with pure HTML markup. The biggest difference is that you have to use the`className` prop instead of `class.` Both the `id` prop and `className` prop take either a string or an expression the evaluates to a string, like this:

```jsx
//JSX
<div
  id="side-bar"
  className={isSmall ? 'side-bar--small' : 'side-bar'}
/>

//DOM
<div id="side-bar" class="side-bar"/>
```

Maintaining CSS at scale can become very challenging so the React community has built tools that improve the developer experience in maintaining CSS at scale. There are several that have been created, but the two biggest are probably CSS modules and CSS-in-JS( and more specifically styled-components/emotion).

CSS Modules allow you to treat a CSS file like a JS Module. You import a CSS file and the classes in the CSS file are treated like properties on a JS object, like this:

```jsx
import css from './index.css';

<div className={css.gridLayout}/>

//DOM
<div class="gridLayout-5xyn87oq5x"/>
```

You’ll notice that the class that was ultimately used was the name of the class plus a hash. This hash is added to the class to ensure that it is unique in our app.

This allows the developer to not need to know the whole scope of the app and how the CSS that we are writing is going to impact the rest of the Cascade. In fact, it’s typical for each component to have its own CSS file associated with it when you are using CSS modules. CSS Modules needs to be enabled as part of a build process, for example in Webpack to be able to take advantage of the tool.

Styled-Component and Emotion are two very popular libraries that fulfill the same goal as CSS modules but goes about it in a different way. Both libraries take advantage of the [tagged template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates) syntax, introduced in ES6. Simply put, tagged template literals are functions that are invoked with a template literal strings. The function parses the template literals and can act accordingly.

How these libraries work is you write CSS as a template string, the function parses the template string, injects it into a style tag of the document, and returns a component with the appropriate class injected into it already. Here is an example of how it works (the example is using styled-components, but emotion uses an almost identical API):

```jsx
import styled from "styled-components";

const Layout = styled.div`
  display: grid;
`;

//DOM
<div class="sc-bZQynM iEpgro" />;
```

As you can see, it reaches the same goal as CSS Module, but rather than creating the scoped class at build time, it builds the CSS at runtime. This allows for many cool features that are beyond the scope of this post, but I would encourage you to check them out the docs for [styled-components](https://www.styled-components.com/docs) and [emotion](https://emotion.sh/docs/introduction).

With all these ways to style, what is the best way to style? Much is subjective and really depends on the developer or developer teams preference. That said, there are some best practices that have emerged.

### Inline Styles are No Longer “Bad”

One of the first things I learned when learning CSS was that inline styles were bad, didn’t scale well, and should be avoided at all cost. But it’s important to understand the reasons why they are considered “bad”.

Inline styles are considered bad because inline styles only apply to that specific element. If you need to reproduce that same style, such as a primary button, you have to copy and paste from one part of your app to another. Now when you factor in sweeping style changes and styles that are only partially the same across two elements, you have a nightmare to maintain.

In a component-based framework, like React, this is not a factor anymore. If you have a need for a styled element in more than one place, you use the same component in both places. If the style changes for both elements, you still only have one component to update so it doesn’t have the same scaling issues as when you work with HTML directly.

Inline styles also allow styles to be configurable via props. You can expose certain elements via props to be configured by the user of your component. If you have style constants that are universally used across your site, these can be maintained in a style constants folder and imported in. Here is an example of how all that can be done:

```jsx
import { white, serif, boxShadow } from "./style-constants";

const Layout = ({ font, ...props }) => {
  return (
    <div
      {...props}
      style={{ fontFamily: font || serif, backgroundColor: white, boxShadow }}
    />
  );
};
```

This method isn’t perfect. Inlines styles don’t give access to pseudo selectors or media queries, for example. But it is not bad to use inline styles anymore and should definitely be part of a developer’s toolbox.

### Components Shouldn’t Position Themselves

In a component-based framework, like React, we separate our concerns at a component level, this includes the styles. A component doesn’t know where it is being rendered nor who it’s siblings are if there are any. It also doesn’t know how it’s being positioned. Its parent might be using flex-box, css-grid, floats or position absolute, for example. So there is no way for you to set values such as margin, position, flex-self, or any other property like that.

The dilemma is that CSS isn’t designed around a component-based framework and many of these properties need to be set at the child level, which means the child needs to “know” about it’s surrounding despite the fact that it doesn’t know.

How do we get around this? There are 3 ways we can do this. One way is to expose the `className` and/or `style` props to allow styles to be injected in, like this:

```jsx
const Widget = ({ className = "", ...props }) => (
  <div className={`widget ${className}`} />
);

//or

const Widget = ({ style = {}, ...props }) => (
  <div style={{ ...defaultStyles, ...style }} />
);
```

The potential downside to this is that it does open up your other styles to be overwritten, which is often undesirable, especially when working within a design system. To protect against this, you can put your styles, that you don’t want to be overwritten, last. You can do it like this:

```jsx
const Widget = ({ className = "", ...props }) => (
  <div className={`widget ${className} non-override`} />
);

//or

const Widget = ({ style = {}, ...props }) => (
  <div style={{ ...defaultStyles, ...style, ...nonOverride }} />
);
```

Another way to do this is to explicitly expose specific values, such as margin or position. Your Component can then do logic to ensure the prop is correct and apply it. This gives you more control but forces you as a Component author to handle each and every prop that you choose to expose.

A third way is to compose your Component in “wrappers” that have that positioning built in, like this:

```jsx
const Layout = () => (
  <LayoutWrapper>
    <MarginBox>
      <Widget />
    </MarginBox>
  </LayoutWrapper>
);
```

This does give more control without having to expose specific fields. But it can cause a lot of indirection when you have a lot of these wrappers all over the place. The best way to combat wrapper hell would be to compose them into their own component, such as using a Higher Order Component.

### Styles Should be Abstracted Away

I went into this once already in my post about [trying to do too much in your React Components](https://medium.com/the-non-traditional-developer/your-react-components-are-doing-too-much-16e65968f419). There are many tools that you can use to add styles to your Component and how those styles are applied are not typically important when you are composing your elements together. Our Components should look less like this:

```jsx
const SideBar = ({ title }) => (
  <div className="container">
    <h2 style={{ color: "grey" }}>{title}</h2>
    <div className="body">
      <Widget />
    </div>
  </div>
);
```

And more like this:

```jsx
const SideBar = ({ title }) => (
  <Container>
    <Title>{title}</Title>
    <Body>
      <Widget />
    </Body>
  </Container>
);
```

The second example is the right level of abstraction when composing the SideBar Component. When composing the SideBar Component I don’t really care **how** the style is added, only that the style **was** added. The first example is much noisier and slows down my ability to understand what the component is doing.

Styling a web page is very important for a web page. The way our web page looks can make or break a website. Luckily we have many tools and best practices that can help us deliver an amazing looking website that our user’s love to use.
