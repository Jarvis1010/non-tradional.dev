---
title: "Building a React Container Query Hook for DOM Elements"
datePublished: Mon Mar 16 2020 14:01:00 GMT+0000 (Coordinated Universal Time)
cuid: ckwokj71j02sd2zs15o2r7l17
slug: building-a-react-container-query-hook-for-dom-elements-ee25cd208740
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316778747/_Co6Z3HL1.jpeg
tags: javascript, web-development, reactjs

---

Photo by [Tim Bennett](https://unsplash.com/@timbennettcreative?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

There have been many great additions to CSS over the years, but if you had to name one that changed the way we wrote web apps, I would argue that media queries have been one of the biggest game-changers in that regard. Before the use of media queries, responsive web design pretty much didn’t exist. Websites typically did one of two things: They either ignored mobile users and built their websites for desktops only or the websites would create a mobile version of their website, often mobile.domain.com, creating an experience optimized for smaller screens.

This was ok, but maintaining two websites at scale was not very practical and more often than not, the mobile site would lag significantly behind in features compared to the desktop counterpart. With the addition of media queries, we could now progressively enhance our website so we can create a great experience no matter what device is used to access it. Media queries have allowed the mobile web to have a seat at the proverbial “grown-up table”. Developers are now empowered to build great experiences for all their users no matter who they are and on which devices they are using.

Then came the age of components and all things have changed. Components have changed the way we look at web development for the better. We no longer need to know the whole context of the web page, but instead, we can work on individual parts of the app. When we work on components, everything that is needed to make those components work is co-located together. That means that all the JavaScript, HTML, and CSS are all bundled together.

With this new power comes a new challenge. [As I wrote in a previous post](https://medium.com/the-non-traditional-developer/styling-best-practices-using-react-c37b96b8be9c), since our components are being built in isolation it is best practice to style from the border inward. This means that your components should not set their own margin nor position themselves. This also means that generally speaking, your component should not set their own width, allowing the parent container to set its width for it.

The challenge comes when you want to build a responsive layout. Simple media queries are now less helpful because no matter what size screen your component is being used in, depending on the parent container, the actual width of the it could be large or small.

If you are like me your first thought would probably be, why don’t they just make container queries? After a quick google search you will find out that this not only has already been considered, it’s a much more difficult problem than it would seem. The problem can be described simply like this. A developer can trigger a layout change in a child element when the parent element changes, which can inadvertently cause the parent to change in size, which will cause the children to change in size and so on and so on. With CSS being a purely declarative language, there isn’t a way for the developer to defend against it, leaving the sole responsibility to the browsers to solve this problem, which is not an easy problem to solve and why it hasn’t been solved yet.

Luckily a new DOM API has been recently added to help solve this problem: the Resize Observer. The Resize Observer, much like it’s cousins the Intersection Observer and the Mutation Observer, allows you to observe a DOM element and run a call back any time the size of the element changes.

The full details of how to use the [Resize Observer can be found over at MDN](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver/ResizeObserver), but of the three observer types, the Resize Observer is the simplest of them. To instantiate an observer you simply pass a callback that will be called when the observed element’s size changes, like this:

```jsx
const observer = new ResizeObserver(callback);
```

Once you have instantiated your observer you simply observe the DOM node you are interested in. You can use the same observer to observe as many nodes as you want and you can stop observing the same element any time. You can also disconnect from all the nodes at once as well, like this:

```jsx
const node1 = document.querySelector("#node1");
observer.observe(node1); //observing node1

const node2 = document.querySelector("#node2");
observer.observe(node2); //observing node1 and node2

observer.unobserve(node2); //observing node1 only

observer.disconnect(); //not observing any nodes
```

So we know how to observe nodes, but what happens when our call back is called? When a node that is being observed is changes size, the call back will be called by passing an array of Resize Observer Entries, [which you can learn more in detail at MDN](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserverEntry), one entry corresponding to each node being observed. We will go more into the properties in a minute, but this is where we can do logic based on the current size of the Node we are listening for.

As of the date of this writing, Safari is the only modern browser that doesn’t support Resize observer yet. Luckily there is a small polyfill that will bridge the gap until Safari does fully support it.

So how do we use a resize observer in a React Hook? Ultimately, what we are going for is something like this:

```jsx
function Layout({ children }) {
  const [matches, ref] = useNodeQuery(320);

  return matches ? (
    <Stack ref={ref}>{children}</Stack>
  ) : (
    <ThreeColumns ref={ref}>{children}</ThreeColumns>
  );
}
```

What we want is a way to know if our container is 320px or less and if it is we will render a Stack Layout, otherwise, it will be a 3 column layout. We don’t have to change the actual component like this, we could just pass a class name or a prop to a component and it will handle that logic that way, but we should also handle the use case where we may need to change the underlying component if the size matches our criteria.

First of all, our hook is going to need to keep track of the node we are observing and if our node is less than or equal to the width passed into our hook. Keeping track of the matches' value is simple enough with a useState hook. In React, when think of keeping track of a DOM node we typically think of the `useRef` hook. So we could write our hook like this:

```jsx
function useNodeQuery(width) {
  const ref = useRef(null);
  const [matches, setMatches] = useState(false);

  return [matches, ref];
}
```

The `useRef` hook allows us to keep track of a specific value in-between renders by returning a ref object who’s value is found on the `current` property. The current property is mutable value and can be changed any time without causing a rerender.

In our use case, however, we don’t want that. We want to know if the DOM Node is changing and rerender accordingly. For this reason, we are going to use a simple `useState` hook like this:

```jsx
function useNodeQuery(width) {
  const [node, setNode] = useState(null);
  const [matches, setMatches] = useState(false);

  return [matches, setNode];
}
```

This works because ref properties on react elements can take either a ref object or a callback. If it is a ref object, it simply changes the current property on the ref object to its ref. If it is a callback, on the other hand, it will pass the ref as an argument to the function. By using our `setNode` function as the ref, it will do two things for us: first, it will update the state in our hook to the ref of the element it is passed to and second, it will tell react that it needs to rerender this node.

Now we need to instantiate our observer and have it start observing. We do this in a useEffect, like this:

```jsx
function useNodeQuery(width) {
  const [node, setNode] = useState(null);
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    const observer = new ResizeObserver(callback);

    observer.observe(node);

    return () => {
      observer.disconnect();
    };
  }, [node]);

  return [matches, setNode];
}
```

Let’s break down what is happening in our useEffect so far. First of all, we are instantiating our observer with a callback, which we will complete in a moment. After that, we are observing the node, and then returning a cleanup function that will disconnect the observer. Because we want to re-run our effect hook if the node changes, we pass it into the dependency array as the second argument of useEffect.

The one problem with the above code is that since we are using the call back style of setting the ref, the effect could be called before the node has been set. To defend against it we just need to adjust our code like this:

```jsx
function useNodeQuery(width) {
  const [node, setNode] = useState(null);
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    let observer;

    if (node) {
      observer = new ResizeObserver(callback);

      observer.observe(node);
    }

    return () => {
      if (observer) observer.disconnect();
    };
  }, [node]);

  return [matches, setNode];
}
```

Now we are only instantiating the observer and observing if the node is not `null` and in our cleanup function, we are checking that the observer is instantiated first before disconnecting.

The final part we need to get our hook working is writing our callback. Let’s look at the callback in isolation, before we put it in the hook:

```jsx
([entry]) => {
  const nodeWidth = entry.borderBox
    ? entry.borderBox.inlineSize
    : entry.contentRect.width;

  setMatches(nodeWidth <= width);
};
```

Our callback takes an array of entries, but since we will only ever be listening to one node at a time, we can destructure off the first entry in the array. Then we need to get the current width of the node. As of the day of this writing, we are in an interesting transition period regarding the spec, [which you can get more details at MDN regarding it](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserverEntry).

Originally, the entry had a property called contentRect, from which you could get the width. The spec is adding two new properties that are intended to replace the contentRect property, borderBox and contentBox, with each property corresponding to a different part of the CSS box model (Once again, [MDN has a very good explanation](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model) of the CSS box model if you need it).

Neither the contentBox nor the borderBox property has a width property, instead, it has an inlineSize property. This keeps the resize observer more consistent with the new [logical properties standard in CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties).

Then as soon as we have the nodeWidth we can simply compare it to the width passed into the query and set that in the setMatches function.

Now our full hook looks like this:

```jsx
function useNodeQuery(width) {
  const [node, setNode] = useState(null);
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    let observer;

    if (node) {
      observer = new ResizeObserver(([entry]) => {
        const nodeWidth = entry.borderBox
          ? entry.borderBox.inlineSize
          : entry.contentRect.width;

        setMatches(nodeWidth <= width);
      });

      observer.observe(node);
    }

    return () => {
      if (observer) observer.disconnect();
    };
  }, [node, width, setMatches]);

  return [matches, setNode];
}
```

With the addition of the callback to our observer, we needed to add two more dependencies to our dependency array: width and setMatches. This will allow our useEffect to keep in sync, not just with the node, but with the width parameter passed in (I put the setMatches in the dependency array per the recommendation of the React Hooks linter).

Now we can build our responsive layout box. To see it in action, check out this code sandbox:

<iframe src="https://codesandbox.io/embed/vhl3d" width="1192" height="596" frameborder="0" scrolling="no"></iframe>

If you open the code sandbox above, you can see that the Layout component we have will switch from a 3 column grid to a stack when it’s width is 320px or less, but then the Main layout shifts when the screen size gets smaller than 545px, which will switch the layout back to a 3 column grid. Now our layout can be responsive to the best version based on its own width, no matter what the screen size is.

Even though we don’t have any indication of when or if container queries will be added to the CSS spec, we can use Resize Observers to serve the same purpose, building responsive layouts at the component level.
