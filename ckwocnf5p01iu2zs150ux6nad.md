---
title: "Create CSS-Only Components with a Prop-Like API Using the AVO Method"
datePublished: Thu Dec 02 2021 02:39:12 GMT+0000 (Coordinated Universal Time)
cuid: ckwocnf5p01iu2zs150ux6nad
slug: create-css-only-components-with-a-prop-like-api-using-the-avo-method
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/XGNPO89fqV0/upload/v1638331316080/jRWmqkSTl9.jpeg
tags: programming-blogs, css-frameworks, css, layout

---

Are you looking for a fast and efficient way to create layouts for your React apps? Look no further than [bedrock-layout. dev](https://bedrock-layout.dev), a layout library built originally for React. But what if you need to write CSS-only components without React? Enter the AVO method, a BEM dialect that uses data attributes instead of class names to create a prop-like API for your CSS. In this article, we'll show you how to use the AVO method to translate React-based components to CSS-based components and provide some good patterns to follow.

## Introducing the AVO method

Luckily, I was introduced to a different way of writing my CSS called the AVO method. The AVO method, which stands for Attribute Values-Objects, is a BEM dialect created by Michael Chan that uses data attributes instead of class names. This is unique in that I can point my selectors to the data attribute and use the value assigned to the data attribute.

A tradional class name approach would have yielded something like this:

```xml
<header class="split hero split-fraction-1/4 center-text" style="--gutter:1rem">
  <!-- Hero content here-->
</header>
```

The above example can be rewritten like this:

```html
<header
  data-bedrock-split="fraction:1/4"
  style="--gutter:1rem"
  class="hero center-text"
>
  <!-- Hero content here-->
</header>
```

At first glance, not much has changed, but you will notice some fantastic benefits immediately after further inspection. First, using a data attribute forces you to keep the styling of the data attribute together, unlike the class-based methodologies that can’t stop you from putting your classes in any order you want. Even more important is that you get that prop-like API we love in our front-end frameworks.

There is no way I can do it justice in this short post, so I highly recommend you check out Michael's fantastic content on the subject:

[https://chan.dev/posts/avo-a-bem-dialect-using-data-attributes/](https://chan.dev/posts/avo-a-bem-dialect-using-data-attributes/)

For the rest of the post, I want to show some good patterns I found when converting my React-based components to CSS-based components.

## How to translate boolean props and props with a fixed number of values

Boolean props are very easy to implement. Much like how you do boolean props in React, you can select based on the existence of the string.

For example, The [`Center` component](https://bedrock-layout.dev/?path=/docs/components-center--api) has two boolean props: `centerText` and `centerChildren`. To convert this to AVO, one needs to select based if that string exists, like this:

```css
[data-bedrock-center~="center-children"] {
  display: flex;
  flex-direction: column;
  align-items: center;
}

[data-bedrock-center~="center-text"] {
  text-align: center;
}
```

Then we can apply it like this:

```html
<div data-bedrock-center="center-text">
  <!-- text alignment will be centered -->
</div>

<div data-bedrock-center="center-children">
  <!-- children will be centered -->
</div>

<div data-bedrock-center="center-text center-children">
  <!-- text alignment and children will be centered -->
</div>
```

You will notice in the above example that we are using `~=`. If we only use `=`, the string must be an exact match. However, `~=` lets us look for any of the space-delimited words to find a match.

Note this will not match on partial words. If I used the string `center-children-vertically`, this would not match. There is a way to do this kind of math, but I only recommend it for a particular scenario, which I will review later.

This strategy also works well if your prop's values are enumerable; in other words, there is a fixed amount of acceptable values. Here I would use a naming pattern where you provide both the prop name and value delimited by a semi-colon: `<prop-name>:<value>`.

For example, [the `Split` component](https://bedrock-layout.dev/?path=/docs/components-split--api) has a fixed set of values for it's `fraction` prop: `1/4`, `1/3`, `1/2`, `2/3`, `3/4`, `auto-start`, and `auto-end`. This would translate to `fraction:1/4`, `fraction:1/3`, and so on.

With this in mind, we can write our CSS like this:

```css
[data-bedrock-split~="fraction:1/4"] {
  grid-template-columns: 1fr 3fr;
}

[data-bedrock-split~="fraction:1/3"] {
  grid-template-columns: 1fr 2fr;
}

[data-bedrock-split~="fraction:1/2"] {
  grid-template-columns: 1fr 1fr;
}

[data-bedrock-split~="fraction:2/3"] {
  grid-template-columns: 2fr 1fr;
}

/* ect */
```

and we can use that in our HTML, like this:

```html
<div data-bedrock-split="fraction:1/4">
  <div />
  <div />
</div>

<div data-bedrock-split="fraction:1/3">
  <div />
  <div />
</div>

<div data-bedrock-split="fraction:1/2">
  <div />
  <div />
</div>

<div data-bedrock-split="fraction:2/3">
  <div />
  <div />
</div>
```

## How to translate props with unknown values

Sometimes props rely on the user to provide a value, and there is no way to control those values ahead of time. In those scenarios, you may want to reach for a custom property. CSS custom properties, also known as CSS variables, let you define your own properties and values, which you can use in your CSS. Often we see these values being declared in the `root`, like this:

```css
:root {
  --my-custom-size: 15px;
  --my-custom-color: dodgerblue;
}
```

The thing is that nothing says can't be declared inline like this:

```html
<div style="--my-custom-size: 15px; --my-custom-color: dodgerblue;"></div>
```

This allows us to use them in a very "prop" like way. For example, The [`Center` component](https://bedrock-layout.dev/?path=/docs/components-center--api) has a prop called `maxWidth` that accepts any CSS length or percentage. We can translate that into a CSS custom property like this:

```css
[data-bedrock-center] {
  max-inline-size: var(--maxWidth, 100%);
}
```

and we can then use that in our HTML like this:

```html
<div data-bedrock-center style="--maxWidth: 75%">
  <div>
    Nulla luctus nisl nec dui auctor volutpat. Phasellus condimentum elementum
    enim in pharetra.
  </div>
</div>
```

Some of you might be saying, wait, inline styles are bad, aren’t they? And the answer to that is: no, they are not bad. CSS follows an exception-based styling where you start with the most general styles and then work down to the most specific styles. Inline styles are meant to be the most specific parts of your styles, the ultimate exception. If you used them for all your styling, it would be bad, but these are being used to make one specific change to a specific element on the page, so no, they are not bad to use in this case.

## Styling based on the existence of a prop

Sometimes you only want to add styles if a prop exists and is not based on its value. For example, the user can use the [`Inline` component](https://bedrock-layout.dev/?path=/docs/components-inline--api) to pass in a CSS length to a `switchAt` prop to define the threshold that the component will switch to a stacking layout. If the threshold isn't provided, I don't even want certain styles applied.

To achieve this in the AVO method, we need to change our selector slightly. Instead of using `~=`, we will use `*=` instead. This will let us search for the existence of a string, no matter if it's part of a larger string. So we can write our AVO style of the `switchAt` prop like this:

```css
[data-bedrock-inline][style*="--switchAt"] {
  flex-wrap: wrap;
}
[data-bedrock-inline][style*="--switchAt"] > * {
  min-inline-size: fit-content;
  flex-basis: calc((var(--switchAt) - (100% - var(--gutter))) * 999);
}
```

The above code is looking for the existence of the `--switchAt` custom property being passed inline to the `style` attribute. If it does, it will apply styles to both the parent and all the children. Otherwise, these styles will not be applied. So now we can write that in HTML like this:

```html
<div data-bedrock-inline style="--switchAt: 45rem">
  <div />
  <div />
  <div />
  <div />
</div>
```

In conclusion, the AVO method is a powerful and flexible way to write CSS that can help you create CSS-only components with a prop-like API. By using data attributes instead of class names, you can keep your styling organized and easy to maintain. We've shown you some good patterns to follow when translating React-based components to CSS-based components, including how to handle boolean props, props with a fixed number of values, and props with unknown values. We hope this article has been helpful and that you'll consider using the AVO method in your next project. Don't forget to check out the [Bedrock Layout CSS version on Github](https://github.com/Bedrock-Layouts/Bedrock/tree/main/packages/bedrock-layout-css/src/components) and [Michael Chan's content on the subject for more information.](https://chan.dev/posts/avo-a-bem-dialect-using-data-attributes/#attribute)