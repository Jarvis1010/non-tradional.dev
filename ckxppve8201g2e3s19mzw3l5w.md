---
title: "Stop Using Left, Right, Top, and Bottom in Your CSS"
datePublished: Thu Oct 01 2020 16:54:44 GMT+0000 (Coordinated Universal Time)
cuid: ckxppve8201g2e3s19mzw3l5w
slug: stop-using-left-right-top-and-bottom-in-your-css-e39905adde65
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316623005/85L0T-Y_-.jpeg
tags: css

---

Photo by [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

When CSS was introduced, it was a game-changer to how we style our web pages. It introduced many things, including the box model, giving us the ability to control properties like margin, border, and padding. Unfortunately, the original spec was created naively, only accounting for written left-to-right and top-to-bottom languages. Setting values like, `margin-top` or `border-left` were intuitive for an English speaking audience but broke down when used in languages like Hebrew, Chinese, Arabic, or Chinese.

This lesson was learned by the time that Flex-box and CSS Grid were developed, so they don’t suffer from the same issues, but what about these original property values? Luckily for us, the CSS working group has revisited those initial property values and added (not replaced) what is now called CSS Logical Properties. For this reason, we all need to stop using the original property values and instead use these new logical properties, so we can build more resilient layouts that can handle the needs of all of our audiences, no matter the language used on their computer.

### Understanding Normal Flow

These new properties are not difficult, but it would be helpful to revisit how normal flow works in CSS to better build intuition regarding these new property names. We are interested in two directions when talking about Normal Flow in CSS: inline and block.

Inline flow is the direction new words are added in a line of text. In English, for example, this is left to right. Vertical languages, like Japanese, would be from top to bottom. Block flow, on the other hand, goes in the perpendicular direction to the inline flow. The best way to think of this is in which direction you read pages in a book. In English, this is from top to bottom. Japanese, on the other hand, reads right to left.

Now that we understand Block and Inline flow directions, we can now understand these new property names. Let’s start with`-left` properties. These include properties like `margin-left` and `border-left.` In the context of Normal flow for the English language, left is the start of the inline flow direction. So anywhere we would usually use `-left` , we should now start using `-inline-start` it instead. For example, `margin-inline-start` and `border-inline-start` should be used now instead. The`-right` properties are easy to translate as well because the right is the end of the inline flow. Therefore we can replace`-right` with `-inline-end,` such as `margin-inline-end` and `border-inline-end`

The remaining properties are pretty easy to figure out now as well. The top and bottom are the start and end of the block flow direction. This means anywhere we used to use `-top` or `-bottom` we can now replace them with the properties `-block-start` and `-block-end` respectively. For example, `margin-block-start` and `border-block-end.`

Many other properties have been updated to use these logical names, and you can find a complete [list of them over at MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties). There are two other properties that I feel are important to mention here as they can impact our day to day layouts. It is common for us to set the `height` and `width` of an element using CSS, but once again, these values are relative to the English-speaking world. What is supposed to be the width or the height of our element if the language is a vertical language like Japanese?

For this reason, `width` and `height` should be replaced by `inline-size` and `block-size` respectively. This includes all the `max-` and `min-` prefixed properties as well. You can set the `max-block-size` and `min-inline-size` instead of the `max-height` and `min-width`

### Browser Support

For anything new, one of the first questions every developer asks is: “What is the browser support like?” There is good news, and there is bad news. The good news is that all modern evergreen browsers, like Chrome/Edge, Firefox, and Safari, all support CSS Logical Properties. The bad news is that IE11, which is still used, does not.

So what do we do if we need to support IE11? In [a previous post on CSS Grid](https://medium.com/the-non-traditional-developer/css-grid-component-patterns-8b472d26fdbe), I talked about how supporting a platform does not mean it needs to be the same experience and that people who use old technology expect a lesser experience.

That said, CSS comes with two wonderful features that you can use to take advantage of these new properties while still supporting old browser users. The first feature is that CSS will let you set a property as many times as you want, and the last assignment of the same specificity wins. For example, if I wrote CSS that looked like this:

```css
.color-change {
  color: red;
  color: olivedrab;
  color: yellow;
  color: dodgerblue;
  color: green;
}
```

The above class's text color would be green since it was the last one to be set.

The second feature is that CSS does not throw an error for invalid lines of CSS. This means that if the CSS parser doesn’t understand a CSS line we wrote, it will ignore that line and keep going. What this means is that if we need to support users who only have access to these older browsers, we can write CSS that looks like this:

```css
.padding-inline {
  padding-left: 1rem;
  padding-right: 1rem;

  padding-inline: 1rem;
}
```

What will happen is that for browsers that support the new CSS Logical properties, we have written those values last so that they will win out. Otherwise, our class will automatically fall back to the traditional `padding-left` and `padding-right` since they were set first, and the new property name will be ignored as invalid CSS.

By the way, I hope you notice one of the really cool features of the new logical property names. If you want to set both inline values or both block values at once, you can use `-inline` or `-block` without the `-start` or `-end` suffix, and it will set both at once. This is much better than using the two-value shortcut method `margin: 0 auto` since this will sometimes cause unintended side-effects by explicitly setting the margin to zero just to set the left and right margins to auto. You can rewrite the above like this: `margin-inline:auto`

The CSS Working group is doing a lot to automatically make it easier for us to support users of all types and languages automatically. By adopting these new logical properties now, we will make our web layouts much easier to maintain as we support users from all over the globe.
