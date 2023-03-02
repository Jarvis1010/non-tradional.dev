---
title: "React Sans JSX"
datePublished: Fri Jul 12 2019 19:29:35 GMT+0000 (Coordinated Universal Time)
cuid: ckxsw6spa09v9v6s1afdickez
slug: react-sans-jsx-37ae49b16e54
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316500873/6quYpZRP_.jpeg
tags: javascript, reactjs

---

Photo by [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the best things to happen to JavaScript would have to be Babel and process by which one could transpile from one version of JavaScript to another. Not only has this allowed the adoption of modern JavaScript to be accelerated, but it has also opened the doors for other important inventions that improve our JavaScript experiences. A wonderful example of this is JSX.

JSX is an XML like structure that allows one to express component rendering in a syntax that is similar to HTML(even though it is not HTML) and therefore in a way that is familiar. Though it is used in other projects, JSX is primarily associated with React. It is how components are built. In fact, it is so common, it might be surprising to some to know the JSX is used outside of React. JSX is not part of ECMAScript (for now) and sending raw JSX to your browser would not work very well. To make it work one depends on a build process to convert JSX into real JavaScript. The most common build process typically uses Babel, Webpack, Typescript, or some combination of those three.

Given all that, what if I told you that you can write React without a build process which means no Babel to transpile JSX into regular JavaScript. React has official docs on using [React without JSX](https://reactjs.org/docs/react-without-jsx.html), but one may ask why would you do that?

There are, in my opinion, two obvious reasons to not use JSX. One is that you have a mostly static web page, and only need to use React to control a widget or a section of the web page that needs functionality. Bringing in a whole build process can be overkill for something that simple. The other reason is that it is good to understand what is happening when your code is transpiled from JSX. The more you understand what’s happening under the hood, the better you are at writing and debugging your application.

So how would you go about doing it? There are a couple of things you need to do to get started. First of all, you will need to bring in React and ReactDOM manually. It’s recommended that you use a CDN to do that. React has [documentation on how to do that at their website.](https://reactjs.org/docs/cdn-links.html) Since you are bringing them in from a CDN, you are no longer going to import React and ReactDOM from your node_modules folder. Instead, you will access them directly from the window object like this:

```javascript
const { React, ReactDOM } = window;
```

From there you are now ready to start writing your React code.

In React, when we write code like this:

```javascript
<div>
  <p>Hello World!</p>
</div>
```

What is really happening is that your transpiler will convert that into this:

```javascript
React.createElement(
  "div",
  null,
  React.createElement("p", null, "Hello World!")
);
```

As you can see, `React.createElement` is how everything gets done. It’s for this reason that you always need to have `import React from 'react'` at the top of your file when you are using JSX. If React isn’t in the scope of the file, then it won't be able to call`React.createElement` to build your file.

Most of the time we write our JSX inside of components. So a component that would have been written like this:

```javascript
const BookTile = ({ imgSrc, tile, authors = [], publishedDate }) => {
  return (
    <div className="tile-container">
      <figure>
        <img
          src="imgSrc"
          alt={`Thumbnail for ${title}`}
          style={{ maxHeight: 150 }}
        />
      </figure>
      <div>
        <h2 style={{ color: "blue" }}>{title}</h2>
        <span>By {authors.join(", ")}</span>
        <span>
          <strong>Published:</strong> {publishedDate}
        </span>
      </div>
    </div>
  );
};
```

Could get really hard to read with so many `React.createElement` on the screen. Due to that, I would recommend that you save the `createElement` function into a variable, like this `const el = React.createElement;` This will make your component much easier to read. For example, the above code would look like this:

```javascript
const { createElement: el } = React;

const BookTile = ({ imgSrc, tile, authors = [], publishedDate }) => {
  return el(
    "div",
    { className: "tile-container" },
    el(
      "figure",
      null,
      el("img", {
        src: imgSrc,
        alt: `Thumbnail for ${title}`,
        style: { maxHeight: 150 },
      })
    ),
    el(
      "div",
      null,
      el("h2", { style: { color: "blue" } }, title),
      el("span", null, "By ", authors.join(", ")),
      el("span", null, el("strong", null, "Published:"), " ", publishedDate)
    )
  );
};
```

Speaking of components, the next logical question comes up, how does one use components if you aren’t using JSX? Luckily the answer is simple, you use `React.createElement` but instead of passing in element name as a string, you simply pass the component itself. For example, to use the above example you would write it like this:

```javascript
el(BookTile, { imgSrc, title, authors, publishedDate });
```

This means we can easily implement concepts such as higher-order components or render prop components pretty simply. Here are two simple examples:

```javascript
//renderProps
el(Translation.Consumer, null, (strings) => {
  //use translation strings
});

//Higher-order Component
const withTranslation = (Component) => (props) =>
  el(Translation.Consumer, null, (strings) => {
    return el(Component, { ...props, strings });
  });
```

JSX is a great tool and it makes your code much easier to read, but it is optional. Depending on your situation, it may be easier to build what you need in React without a build process and without JSX. Ultimately, as you try it you will have a much deeper understanding of how to React works and be a better developer for it.
