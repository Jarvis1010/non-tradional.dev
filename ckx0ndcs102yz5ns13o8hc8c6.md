---
title: "Encapsulated CSS: The Key to Composable Layouts"
datePublished: Thu May 13 2021 21:23:45 GMT+0000 (Coordinated Universal Time)
cuid: ckx0ndcs102yz5ns13o8hc8c6
slug: encapsulated-css-the-key-to-composable-layouts-94f11c177cc1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316648320/6VuS9VzcK.jpeg
tags: css, javascript, reactjs, layout

---

Photo by [Sigmund](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

We have entered the Age of Components. Most frameworks, like React, use components as their foundation. They do this for a few reasons, but a crucial one is that components allow you to break your app into simple single-purpose parts that can then be composed together to solve more complex needs.

Unfortunately, CSS was invented to solve problems from the top down, [starting with more general rules to more specific rules](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Organizing#create_logical_sections_in_your_stylesheet). On the other hand, components encourage you to start from the bottom up, breaking your pages down into the more specific parts first and then composing them together to build up to the whole.

Many tools and naming conventions have been created to help us maintain our style sheets like BEM, SASS, Less, CSS-Modules, and CSS-in-JS, but they all fall short in the one problem that tooling can never solve: **Which component should be in charge of which styles?**

The answer to this question is key to making composition work, especially where web layout is concerned. That answer is Encapsulated CSS.

### **What is Encapsulated CSS**

Encapsulated CSS is a term I use to summarize the rules of how to apply my CSS styles in a composable way. It’s based on the programming principle of encapsulation. Here is my best attempt to define encapsulation in a language-agnostic way: Encapsulation involves grouping related things and restricting access to those things except through appropriate channels.

For example, many program languages utilize a module system, which follows the principles of encapsulation.

```javascript
import React from “react”;
```

When you import a module like `React`, you get a group of functions that help you build React applications, but you don’t have access to the actual internals that make React applications work.

Encapsulated CSS is based on that same principle. It’s a methodology that helps you group together related styles at the correct component level and only applying styles through appropriate channels.

### The Two Principles of Encapsulated CSS

There are two essential principles of Encapsulated CSS:

The first principle is: **“Components do not lay themselves out.”** When I say that components don’t lay themselves out, I am specifically speaking to a component’s position, size, and margin.

```css
/* Not ok to set on the component */
component {
  display: inline;
  width: 100px;
  margin-top: 2rem;
  position: relative;
}
```

The second principle is: **“Components style themselves and layout only their immediate children.”** Properties that involve the border-box and inward are considered part of the component and should be applied at this level. This also includes the layout environment of the component’s immediate children.

```css
/* Ok to set on the component */
component {
  border: 1px solid #08228c;
  color: #4c6ef5;
  padding: 3rem;
  font-family: sans-serif;
}
```

### Using Appropriate Channels

If a component shouldn’t set its own layout properties, how do you set them? I mentioned earlier, that encapsulation allows you to access properties through appropriate channels. These appropriate channels in components are `props` and the direct child combinator.

In React, we use `props` as inputs to our component, much like functions use arguments. Just like we can expose a `label` or a `onClick` prop, we can expose layout properties like `margin-top` or `min-width`:

```javascript
export const Component = (props) => (
  <div style={{ minWidth: props.minWidth }}>
    {/* Component content goes here */}
  </div>
);
```

Using props in this manner works well with one or two properties, but it becomes unwieldy very quickly as you expose more and more properties. The props of your component should also be a reflection of what your component does. Having an arbitrary `marginLeft` prop doesn’t make sense on a calendar component.

The other channel for adding styles is using the direct child combinator in the parent component.

```css
.parent > * {
  margin: 1rem;
}
```

This tool allows us to select any or all of the immediate children and apply the layout styling we need.

### **A Blog Post Component**

Let’s take the following Blog Post component:

```jsx
export const BlogPost = (props) => {
  return (
    <article className="blog-post">
      <h2 className="blog-title">{props.blogTitle}</h2>
      {props.paragraphs.map((paragraph, i) => {
        return <p key={i}>{paragraph}</p>;
      })}
    </article>
  );
};
```

Applying styles that follow the principles of Encapsulated CSS we could do something like this:

```css
.blog-post {
  padding: 1rem;
}

.blog-post > h2 {
  margin-bottom: 3rem;
  max-width: 75%;
}

.blog-post > p + p {
  margin-top: 2rem;
}

.blog-title {
  text-transform: uppercase;
  font-size: 1.5rem;
  color: darkgray;
}
```

In the above stylesheet, the `.blog-post` and `.blog-title` classes set their own styles. When we needed to set layout properties, we used the direct child combinator to select the appropriate children of the `.blog-post` to set those properties.

At this point, one might start to ask why bother to go through this ceremony of adding a separate rule for the direct children just to add a layout property?

First of all, because we didn’t add any layout properties to the `.blog-post`, this blog post component can safely be placed in any context and not interfere with the layout environment it is being put in. The children can also now be refactored into components, if needed, and won’t bring along the baggage of the layout environment it was initially created in.

It also helps with debugging. One of the most significant difficulties of debugging CSS is determining where your styles are coming from. It is easier to track down styles if you have rules in place where those style properties originate.

UItimately, Encapsulated CSS, is about creating restrictions that makes sense. By following these rules, your components will become much easier to maintain and compose anywhere in your app.
