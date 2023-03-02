---
title: "Creating Reliable Web Apps: Checking User Connectivity with React Hook"
datePublished: Fri Aug 09 2019 21:45:33 GMT+0000 (Coordinated Universal Time)
cuid: ckxslbedl076du5s14a6t2zoo
slug: checking-the-network-connection-with-a-react-hook-ec3d8e4de4ec
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316540208/GIXwwYnHj.jpeg
tags: javascript, reactjs, reacthooks

---

Photo by [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Are you a front-end developer looking to create delightful user experiences that work seamlessly, even when users are offline? With mobile web usage on the rise and Wi-Fi dead zones still a reality, it’s more important than ever to check if your client is online. In this article, we’ll show you how to use a React Hook to determine if your user is online or not, using the Navigator object in JavaScript. Read on to learn more!

In a world of high-speed dedicated internet connections becoming increasingly commonplace, one of those often-overlooked experiences is what to do in your experience based on if the client is no longer online. Often times we just take for granted that we will be online, but that isn’t always the case. More and more web usage is being handled over mobile phones, which can never be reliable. Wifi is getting better, but Wifi dead zones do exist. Even hard-wired people can be a broken utility cable away from being kicked off that dedicated high-speed connection.

The point of this article is not to get into the UI/UX on what best practices you should do when a client is no longer online. Instead, I intend to get you past the most important hurdle of them all: Determining if you are online in the context of a React Component.

### The Navigator Object

Before we get into implementing this in a hook, I think it’s important to understand how JavaScript can determine if it is online or not. This information is found using the Navigator object. What is the Navigator object? Simply put it is an object of read-only data about the state and identity of the specific browser using your data. It has properties such as geolocation, userAgent, and many other things, including whether you are online. As always, I would recommend checking out the [documentation found over at MDN regarding the Navigator object](https://developer.mozilla.org/en-US/docs/Web/API/Navigator).

You access the Navigator object from the global window object like so: `window.navigator` and from there, you can access one of the many properties it has available. The specific property we are looking for is the `onLine` property. Take special note of the casing. It’s NOT online; it is camel-cased, `onLine`.

### Using It In a Hook

The obvious first thing we need is some state to keep track of whether we are online and return that from our custom hook:

```javascript
import { useState } from "react";

function useNetwork() {
  const [isOnline, setOnline] = useState(window.navigator.onLine);

  return isOnline;
}
```

This is good for when the component mounts, but what happens if the client goes offline after it mounts? Luckily there are two events we can listen for that we can use to trigger an update to your state. To do this, we need the useEffect hook like this:

```javascript
function useNetwork() {
  const [isOnline, setNetwork] = useState(window.navigator.onLine);

  useEffect(() => {
    window.addEventListener("offline", () =>
      setNetwork(window.navigator.onLine)
    );

    window.addEventListener("online", () =>
      setNetwork(window.navigator.onLine)
    );
  });

  return isOnline;
}
```

As you can see, we are listening to two events, `offline` and `online` and then updating the state when the event is triggered. Anyone who has dealt with hooks and event listeners will see two obvious problems. The first is that we need to return a cleanup function from this useEffect callback so that React can remove our event listeners.

The second is to remove an event listener; you need to provide the same function, so it knows which listener to remove. Passing in another arrow function that looks the same is not going to remove the event listener even if the function ‘looks’ and ‘acts’ the same. So here is how we will update our hook:

```javascript
function useNetwork() {
  const [isOnline, setNetwork] = useState(window.navigator.onLine);

  const updateNetwork = () => {
    setNetwork(window.navigator.onLine);
  };

  useEffect(() => {
    window.addEventListener("offline", updateNetwork);

    window.addEventListener("online", updateNetwork);

    return () => {
      window.removeEventListener("offline", updateNetwork);

      window.removeEventListener("online", updateNetwork);
    };
  });

  return isOnline;
}
```

Using a React Hook and the Navigator object in JavaScript, you can easily determine if your user is online or not, and provide a unique experience for them depending on their connectivity status. By implementing this feature, you can create more robust and reliable web applications that work seamlessly, even in challenging network conditions. We hope this article has been helpful in guiding you through the process of checking the network connection in your React components.