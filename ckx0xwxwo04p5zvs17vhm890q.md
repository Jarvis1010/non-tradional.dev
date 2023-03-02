---
title: "Normal Flow: The Default Web Layout"
datePublished: Mon Apr 19 2021 14:03:26 GMT+0000 (Coordinated Universal Time)
cuid: ckx0xwxwo04p5zvs17vhm890q
slug: normal-flow-the-default-web-layout-3126c129e5a3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316644427/1ygtREPw8.jpeg
tags: css

---

Photo by [Mike Lewis HeadSmart Media](https://unsplash.com/@mikeanywhere?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

It can be easy to think that there is no layout by default on the web, but that would be incorrect. CSS has a default layout called Normal Flow that defines how elements interact with each other on the page when no styling is applied.

### **Block-Level**

One of the best ways to think about how the block formatting context works is to think of paragraphs on a page. Paragraphs are always as wide as the page that contains them but are only as tall as the content in each paragraph. Paragraphs also stack on top of each other, with each new paragraph appearing below the previous one.

Similarly, elements in a block formatting context stack vertically one after the other, take up their container’s width and grow in height according to their content. When a block-level element is nested inside another block-level element, it will take up the parent element’s width. It is also important to remember that even if the width of two block-level elements is changed to allow them to fit next to each other, they will continue to stack vertically.

Here is an example of block-level elements:

```html
<html>
  <head>
    <title>Block Example</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <h1>Block-Level Elements</h1>

    <p>All Elements in here are block-level elements</p>

    <section style="width: 33%">
      <h2>Nested Block Elements</h2>
      <p>
        Block elements nested inside of other block elements will naturally fill
        the width of their block container.
      </p>
    </section>

    <section style="width: 33%">
      <h2>Block Elements Always Stack</h2>
      <p>
        Even if two block-level elements can fit next to each other, they will
        continue to stack on top of each other
      </p>
    </section>
  </body>
</html>
```

Commonly used elements that default to the block formatting context include the `p` tag, `h1`—`h6` tags, `div` tag, `section` tag, `article` tag, and all the list tags, including `ul`, `ol`, and `li` tags. For a complete list of block-level elements, you can refer to [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements#elements).

### **Inline-Level**

In a similar vein, elements in an inline formatting context can be compared to words in a sentence. By default, words are only as wide as their content. Words in a sentence are added horizontally, left to right, and will wrap to the next line when there is no more available space to add another word.

Elements in the inline formatting context behave the same way. Inline elements are placed horizontally, one after the other, from left to right. By default, inline elements are as wide as their content and will wrap to the next line when there isn’t enough space for the element.

For example:

```html
<html>
  <head>
    <title>Inline Example</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <h1>Inline Elements</h1>
    <div>
      All elements in this `div` are inline elements. Not only does this include
      elements like <span>span elements</span>,
      <strong>strong elemenst</strong>, and <em>em elements</em>, it also
      includes <label>form labels</label>, form inputs <input />, images
      <img src="http://placekitten.com/100/16" alt="" /> and iframes.
      <iframe src="https://www.newline.co" />
      Each inline-level element will be placed from left to right until it runs
      out of space, at which point it will wrap onto the next line below.
    </div>
  </body>
</html>
```

Commonly used elements that default to the inline formatting context include the `span` tag, the `strong` tag, the `em`tag, the `img` tag and the `iframe`. Bare text, text not wrapped in an element, is treated as if it is wrapped in an inline element. For a complete list of block-level elements, you can refer to [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements#elements).

### **Margin and Padding in Normal Flow**

We know how the block and inline elements are placed, but how do they interact with space, namely, what happens when margin and padding are applied to our elements?

First of all, a quick recap of padding and margin and how they are different. Padding is the space between the border and the content. Adding padding to an element increases that element’s size, similar to how a balloon expands in size when you blow it up. Margin, on the other hand, pushes away from other elements. Increasing the margin has no impact on the size of the element, only just the extent it pushes against other elements.

Block-level elements let you define their padding and margin, but when two block-level elements appear one after the other, their vertical margins will collapse into one margin. The net margin will never be less than the greater of the two values. For example, a 10-pixel margin at the bottom of one block-level element and a 30-pixel margin at the top of the next block-level element will collapse together into a single 30-pixel margin between them. All other margins and padding values behave as expected.

On the other hand, inline-level elements are much more unique in how they handle margin and padding. Margin is only honored in the horizontal direction. Margin applied to the top and bottom are ignored. To change the space in between lines, you need to adjust the line-height property.

Padding is even more unique. Just like margin, padding only visibly increases in the horizontal direction. Padding applied to the top and bottom of the element will not visibly grow; however, it still grows. If you use a background or border, it will creep into the other lines.

While we are talking about margins and default behavior, it is essential to point out that certain elements will have margin and padding applied by default. Browsers implement these values rather than being defined in the CSS Specification itself.

```html
<html>
  <head>
    <title>Margin and Padding</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <h1>Margin and Padding</h1>

    <section style="width: 66%">
      <h2>Block-Level Margin Collapse</h2>
      <p style="margin-bottom: 10px">
        This paragraph has a 10px margin-bottom and the other paragraph has a
        30px margin-top. However the actual margin between them will only be
        30px.
      </p>
      <p style="margin-top: 30px">
        This is because a block-level elements will collapse it's vertical
        margin with other block-level elements. All other padding and margin
        will behave as expected.
      </p>
    </section>

    <section style="width: 66%">
      <h2>Inline Margin and Padding</h2>
      <p>
        Inline elements only honor the horizontal
        <span style="margin: 2rem">margin</span> while vertical spacing is
        controlled via the `line-height` property.
        <span style="padding: 2rem">Padding</span> can give the impression that
        it behaves the same way. However, if you apply a
        <span style="padding: 2rem; border: 1px solid black">border</span> or
        <span style="padding: 2rem; background: rgba(30, 144, 255, 0.3)"
          >background</span
        >
        you will see that the size does increase and encroaches into the other
        lines.
      </p>
    </section>
  </body>
</html>
```

Even though you are not explicitly setting a layout, your HTML elements have an implicit layout structure that they follow. Understanding how elements interact with each other by default is essential to know how to compose layouts together.
