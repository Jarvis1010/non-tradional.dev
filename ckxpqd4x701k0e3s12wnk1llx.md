---
title: "The Ultimate Responsive Grid Component in React"
datePublished: Thu Oct 08 2020 14:01:07 GMT+0000 (Coordinated Universal Time)
cuid: ckxpqd4x701k0e3s12wnk1llx
slug: the-ultimate-responsive-grid-component-in-react-d4c72ebffb57
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316603708/gBQuPmqck.jpeg
tags: css, javascript, reactjs, styled-components

---

Photo by [leander li](https://unsplash.com/@leanderli?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Over a [few of my previous blog posts](https://medium.com/the-non-traditional-developer/css-grid-component-patterns-8b472d26fdbe), on CSS Grid Component Patterns, I have been showing you how to take advantage of CSS Grid in a composable componentized way. We have built some very simple components like a Stack and Split. We have then expanded upon that knowledge to build some more complex compound components in the Columns and Column component. There is one more very useful component that we can build using CSS Grid that solves one of the biggest challenges of CSS layout: A responsive grid of items. The component we are going to build today is simply called The Grid and creates a nice responsive grid of items using only 3 lines of CSS.

### The Grid Component

In the [Columns Component](https://medium.com/the-non-traditional-developer/more-css-grid-component-patterns-60d1603fcf75), we learned about the repeat function and how to create n number of items of a particular size. What if we don’t know how many columns we need in advance and we just want to put in the optimal amount of columns based on the size of the container? Luckily we can still use the repeat function to do just that, with the help of some other functions.

Just so you remember, we can create a 3 equal column layout like this:

```javascript
const Grid = styled.div`
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(3, 1fr);
`;
```

Instead of 3 equal columns, we just want a range of what the width can be. To do this we use the CSS function `minmax()`which allows you to tell the browser the minimum that the width can be and the max. This lets the browser have control to optimize the width, as long as it is between the two values. The `minmax` function takes any valid CSS size value, including the CSS Grid `fr` unit.

So we can change our Grid component like this:

```javascript
const Grid = styled.div`
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(3, minmax(320px, 1fr));
`;
```

This is a great start, but we are still specifying that we want 3 columns. What we need is a way to tell the browser to optimize the number of columns, based on the range we specify. Luckily, the repeat function doesn’t just take an integer to indicate how many times to repeat the pattern. Instead, you can pass in `auto-fit` and this will tell let the browser do exactly what we want, optimize our grid columns based on the container size.

Now our component looks like this:

```javascript
const Grid = styled.div`
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
`;
```

There is now only one issue with this. What happens if our container is less than 320px? With the way we wrote it above this would cause an unwanted overflow. To fix this we simply need to use the `min()` function, which does exactly what it says: returns the smallest value provided. We can rewrite our component like this:

```javascript
const Grid = styled.div`
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 320px), 1fr));
`;
```

This now will guarantee that the minimum value is no larger than 100% width of the container. This is component is working great, but it only works if we want a minimum item width of 320px. In the real world, we need to be able to control and fine-tune that item width depending on the content. So let update it to allow it to be more configurable:

```javascript
const Grid = styled.div`
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(
    auto-fit,
    minmax(min(100%, ${(props) => props.minItemWidth}px), 1fr)
  );
`;
```

Now, instead of hard coding the minimum item width to 320px, we can pass in width, making our component useable in any circumstances

There we have it. The final CSS Grid Component pattern. Now there is much more to CSS Grid than these 3 lines, but with these three lines, we have been able to make multiple Layout components that can be composed together to build some pretty advanced layouts. As I have mentioned before, I have created a Layout Library, called [Bedrock Layout Primitives](https://bedrock-layout.dev) and one of the Components available is [The Grid Component](https://bedrock-layout.dev/?path=/docs/components-grid--api). Check it out, use it, and please contribute to it.
