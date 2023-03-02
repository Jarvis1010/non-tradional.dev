---
title: "More CSS Grid Component Patterns"
datePublished: Wed Aug 19 2020 14:01:06 GMT+0000 (Coordinated Universal Time)
cuid: ckwxa2m2h0cj4uws1ai64gaiv
slug: more-css-grid-component-patterns-60d1603fcf75
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316730542/HfFewdoTf.jpeg
tags: css3, javascript, reactjs

---

Photo by [Ben Hershey](https://unsplash.com/@benhershey?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

CSS Grid has changed the way we think about layout on the web. Components have changed the way write code for the web. Put them together they are better than peanut butter and chocolate. [In a previous post, I went over the two patterns you can use when building layout components with CSS Grid](https://medium.com/the-non-traditional-developer/css-grid-component-patterns-8b472d26fdbe): The Stack and The Split components. In this post, we will be going into two more layout components using CSS Grid

### The Columns and The Column Component

One of the longest-standing traditions in CSS frameworks is the concept of a twelve column grid. It opened up the world to a real, non-document flow-based layout. So intuitively might want to replicate that pattern using CSS Grid. This can easily be achieved with two components: the Columns and the Column component.

In the previous article, we learned about how to make two columns in the Split component supplying two CSS length values to the `grid-template-columns` property. So we could simply use that same logic and write something like this:

```javascript
const Columns = styled.div`
   display: grid;
   gap: 1rem;
   grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
`

<Columns>
  {/* content here */}
</Columns>
```

The above code does work, but luckily a new CSS function was added to improve the developer experience, specifically the `repeat` function. The `repeat` function takes two values. The first value is the number of times you want to repeat a CSS length value and the second is the length value you want to repeat. So we can rewrite the above code like this:

```javascript
const Columns = styled.div`
   display: grid;
   gap: 1rem;
   grid-template-columns: repeat(12, 1fr);
`
<Columns>
  {/* content here */}
</Columns>
```

The good thing about our Columns component is that we are no longer bound to just 12 columns. We can have a 5 column layout or a 21 column layout or any number of columns. All we need to do is add a columns prop where we can specify how many columns we want, like this:

```javascript
const Columns = styled.div`
   display: grid;
   gap: 1rem;
   grid-template-columns: repeat(${props=>props.columns}, 1fr);
`
<Columns columns={5}>
  {/* content here */}
</Columns>
```

It’s good and all to have N columns, but often one needs to span more than one column. That is where the `Column` component comes in. With this component, we can take advantage of another CSS Grid property called the `grid-column` property.

Unlike the other properties that we discussed, the `grid-column` property is used on grid-items and not on grid-containers. [There is complete documentation on the property at MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-column) but simply put, the property allows you to define the size and location of a grid-item.

There are a few ways to do that, but for our purposes, we are looking to define how many columns our item spans and to do that we pair the `grid-column` property with the `span` keyword to define how many columns our item will span. To make a column span 3 columns we write our component like this:

```javascript
const Columns = styled.div`
  grid-columns: span 3;
`;
```

Now, of course, we don’t want to hard code our Column component span 3 columns so let us make it more dynamic:

```javascript
const Columns = styled.div`
  grid-columns: span ${(props) => props.span};
`;
```

And now we can use them together like this:

```javascript
<Columns columns={5}>
  <LeftNav />
  <Column span={3}>
    <Main />
  </Column>
  <SideBar />
</Columns>
```

Now we can have the flexibility to create the column layout we need, without being forced to bring in a heavy 12-column grid system. There is one more pattern that CSS Grid can give you, but that will need to be for another day.

As I have mentioned before, I have built a layout component library over at [bedrock-layout.dev](https://bedrock-layout.dev/src-components-columns). There you will find several layout components, including the two above components. I would be very grateful if you would check them out and help me improve them.
