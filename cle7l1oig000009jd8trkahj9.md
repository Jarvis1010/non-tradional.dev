---
title: "Resizing Elements with Confidence: How to Fix ResizeObserver API"
datePublished: Thu Feb 16 2023 20:56:52 GMT+0000 (Coordinated Universal Time)
cuid: cle7l1oig000009jd8trkahj9
slug: resizing-elements-with-confidence-how-to-fix-resizeobserver-api
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/fr3YLb9UHSQ/upload/v1669821044163/1Nb9gGLCN.jpeg
tags: javascript, web-development, reactjs

---

The ResizeObserver API is a relatively new feature in JavaScript that allows developers to detect changes in the size of an element on a web page. This can be incredibly useful for creating responsive designs and optimizing the performance of your website. However, the API may not be intuitive oh how best to use it in modern frameworks like React.

In this post, we'll take a closer look at the ResizeObserver API and how it can be improved to make it easier to use in your projects.

## The ResizeObserver API

The ResizeObserver API provides a way to observe changes in the size of a DOM element. It creates a new ResizeObserver object, which can then be used to observe a specific element. The observer will be notified whenever the size of the observed element changes and can take appropriate action in response.

To use the ResizeObserver API, you'll first need to create a new ResizeObserver object. You can do this by calling the ResizeObserver constructor, passing in a callback function that will be called whenever the size of an observed element changes.

```javascript
const observer = new ResizeObserver(entries => {
  for (let entry of entries) {
    console.log(`Element: ${entry.target}`);
    console.log(`Width: ${entry.contentRect.width}`);
    console.log(`Height: ${entry.contentRect.height}`);
  }
});
```

Once you have created your observer, you can use the `observe()` method to start observing a specific element. This method takes a single argument, which is the element you want to observe.

```javascript
const myElement = document.querySelector('.my-element');
observer.observe(myElement);
```

It's also possible to observe multiple elements at once by calling the `observe()` method.

```javascript
const elements = document.querySelectorAll('.my-element');
observer.observe(elements);
```

The ResizeObserver API also provides a way to stop observing an element using the `unobserve()` method. This method takes a single argument, which is the element that you no longer want to observe.

```javascript
observer.unobserve(myElement);
```

You can also stop observing all elements by calling the disconnect() method on the observer.

```javascript
observer.disconnect();
```

## What's the Problem, then?

The ResizeObserver API provides a powerful and efficient way to detect changes in the size of an element on a web page. Still, it is designed to be a one Observer fits all scenario. The callback we provide will be given all the observed elements resizing, and then, depending on the element, you can make various choices.

In practice, you are either creating too many resize observers because you want to run a unique callback for each element, like this:

```javascript
const mainObserver = new ResizeObserver(()=>{})
mainObserver.observe(mainElement)

const sideBarObserver = new ResizeObserver(()=>{})
sideBarObserver.observe(asideElement)

// ect.
```

Or you stuff a whole bunch of logic into this huge monolithic observer function like this:

```javascript
const monolithicObserver = new Resize Observer(entries=>{
    const entries.forEach(entry=>{
        /**
        * check each entry and run a unique set of code for 
        * Matching entries
        */
    })
})
```

Neither option is ideal. So I created an abstraction that lets me have the best of both worlds. It's called `@bedrock-layout/register-resize-callback`, and it's part of my Bedrock Layout Primitives library, found over at bedrock-layout.dev.

What is unique about this package is that it lets me register a special callback for each element I want to observe without having to instantiate multiple Resize Observers. In a future blog post, we can go into how it works, but for now, let's look at how you use it:

```javascript
import { init, registerCallback } from '@bedrock-layout/register-resize-callback';

// initialize in a safe way when you know you are in the browser
init()

const header = document.querySelector('header');
const article = document.querySelector('article');

// register a callback for each node
// This will return a cleanup function
const cleanupHeader = registerCallback(header, (entry) => {
  console.log(entry);
})

const cleanupArticle = registerCallback(article, (entry) => {
  console.log(entry);
})

//unregister the callback
cleanupHeader()
cleanupArticle()
```

In the above code, we call the `init` function as soon as possible to initialize the `ResizeObserver` that our register callback depends on. This gives you the control you need to ensure that the initialization only happens in the browser. The init function only needs to be called once, but it can be called multiple times without consequences.

Once we are confident that everything is initialized properly. Then we use the `registerCallback` function to register a callback for each node we want to observe. If that element is resized, our callback will be called with the associated entry for that element. This allows your callback to focus on just that element you want to observe without worrying about anything else.

Finally, when we no longer want to observe those elements anymore, we can call the cleanup function that is returned from \``` registerCallback` `` which will both unregister our function and unobserve the element if no other callbacks have been registered on that same element.

Let's say you wanted to observe an element in a React application. Your component might look something like this:

```javascript
import React, {useRef, useEffect} from 'react'
import { init, registerCallback } from '@bedrock-layout/register-resize-callback';

export function MyComponent(){
  const ref = useRef(null);
  const currentDiv = ref.current
  useEffect(() => {
    init();
    if(currentDiv){
     return registerCallback(currentDiv, () => {
        // do stuff
      })
    }
  },[currentDiv]);

  return <div ref={ref}>{/* stuff goes here*/}</div>
}
```

The ResizeObserver is powerful and allows you to do fantastic and dynamic things. Using the `@bedrock-layout/register-resize-callback` package makes it even more intuitive and practical in your apps.