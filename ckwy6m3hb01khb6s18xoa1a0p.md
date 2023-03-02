---
title: "The Evolution of a Custom React Hook"
datePublished: Mon Nov 22 2021 18:13:11 GMT+0000 (Coordinated Universal Time)
cuid: ckwy6m3hb01khb6s18xoa1a0p
slug: the-evolution-of-a-custom-react-hook-3fc0b1f2a6d7
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316711210/suq--2SBg.jpeg
tags: javascript, reactjs, reacthooks

---

Photo by [Suzanne D. Williams](https://unsplash.com/@scw1217?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Abstracting code is a common thing we do in programming. It is also very common for those abstractions to evolve as the requirements change. Recently I had to opportunity to rapidly evolve a React hook from one form to another in the same day. Now that I have finished it, I thought I would share that evolution with you.

The first requirement was that I needed a generic way to poll data in a React component. If you are not familiar with the term, polling means checking data at a regular interval. This first version looked like this:

```javascript
import { useRef, useEffect, useLayoutEffect } from "react";

export function usePolling(callBack, intervalTime = 10_000) {
  const callBackRef = useRef(callBack);

  useLayoutEffect(() => {
    callBackRef.current = callBack;
  });

  useEffect(() => {
    const interval = setInterval(() => callBackRef.current(), intervalTime);

    return () => {
      clearInterval(interval);
    };
  }, [callBackRef, intervalTime]);
}
```

In the above hook, we take a callback and an optional interval time. This callback will be called at a regular interval determined by the interval time provided in the second parameter, set to 10,000 by default.

The first thing that we are doing is using the `useRef` hook to create a `callBackRef` and then we are using the `useLayoutEffect` to keep the ref current with any changes to the callback. Kent C Dodds well documents this pattern, but to summarize, This pattern allows the callback to stay up to date without causing the useEffect to have to rerun or requiring the user of the hook to wrap the callback in a `useCallback` hook.

In the second part of the hook, we are using a `useEffect` hook to start our interval, and then we clean up the hook in the clean-up function returned from the useEffect.

This hook works great, but then I got a new requirement that it needs only to poll when the tab is visible; this would ensure that we are not wasting resources polling data when the user isn't actively using the tab.

Luckily there is an easy way to do this. First of all, the `document` object has a `visibilityState` that will let you know if it is `visible` or `hidden.` There is also an event listener that we can tap into called `visibilitychange` that we can use to react to the visibility state changing. [Both of these are well documented at MDN.](https://developer.mozilla.org/en-US/docs/Web/API/Document/visibilitychange_event)

So let's make a tweek to our hook:

```javascript
import { useRef, useEffect, useLayoutEffect } from "react";

export function usePollWhenVisible(callBack, intervalTime = 10_000) {
  const callBackRef = useRef(callBack);

  useLayoutEffect(() => {
    callBackRef.current = callBack;
  });

  useEffect(() => {
    let interval;

    if (document.visibilityState === "visible") {
      interval = setInterval(() => callBackRef.current(), intervalTime);
    }

    const handleVisibilityChange = () => {
      clearInterval(interval);
      if (document.visibilityState === "visible") {
        interval = setInterval(() => callBackRef.current(), intervalTime);
      }
    };

    document.addEventListener("visibilitychange", handleVisibilityChange);

    return () => {
      clearInterval(interval);
      document.removeEventListener("visibilitychange", handleVisibilityChange);
    };
  }, [callBackRef, intervalTime]);
}
```

In the above code, we have changed the internals of our `useEffect` hook. First of all, we start by declaring the `interval` variable without defining it, and we declare it using a `let.` The reason we are doing this will be more apparent why we are doing as we go.

The next thing we are doing is checking if our current visibility state is `visible` and if it is, we start our interval just as before. This will ensure that we don't begin our interval if the page is no longer visible when the component mounts.

Next, we are defining our event listener function: `handleVisibilityChange.` This function will be called whenever the visibility state changes. In this function, the first thing we are doing is clearing the interval. This is safe because if the value passed in is not a valid interval, the function will ignore it. After we earn the interval we are doing the same visibility check as above and conditionally starting the interval if the visibility state is `visible.`

Finally, we add our event listener function to the `visibilitychange` event.

In our clean-up function, we do two things now. First, we clear the interval to ensure that we don't have a memory leak, but secondly, we also remove our event listener so it won't run.

Our hook is working fantastically now, but then a new requirement came down from the requirements gods. This hook will be used in multiple components, but we need them all to be on the same interval cycle. In other words, we need all our hooks to be synchronized.

To do this, I made this last final set of changes:

```javascript
import { useEffect } from "react";

const INTERVAL_TIME = 10_000;

let callbacks = [];

let interval;

let handleVisibilityChange;

function init() {
  if (handleVisibilityChange) return;

  interval = setInterval(
    () => callbacks.forEach((fn) => void fn()),
    INTERVAL_TIME
  );

  handleVisibilityChange = () => {
    clearInterval(interval);
    if (document.visibilityState === "visible") {
      interval = setInterval(
        () => callbacks.forEach((fn) => void fn()),
        INTERVAL_TIME
      );
    }
  };

  document.addEventListener("visibilitychange", handleVisibilityChange);
}

function cleanUp() {
  if (callbacks.length > 0) return;

  if (handleVisibilityChange) {
    document.removeEventListener("visibilitychange", handleVisibilityChange);
  }

  handleVisibilityChange = undefined;
  clearInterval(interval);
}

export function usePollWhenVisible(callback) {
  useEffect(() => {
    init();

    callbacks = callbacks.concat(callback);

    return () => {
      callbacks = callbacks.filter((fn) => fn !== callback);

      cleanUp();
    };
  }, [callback]);
}
```

Let's break all this down, part by part.

```javascript
const INTERVAL_TIME = 10_000;
let callbacks = [];
let interval;
let handleVisibilityChange;
```

First, we lifted the `interval` and `handleVisibilityChange` functions in the module and out of the hook itself. We have also declared two new variables: `callbacks` and `INTERVAL_TIME.`

The `callbacks` variable is initialized with an empty array. This is where we will maintain our list of callbacks to run each interval cycle. Since we now need all the callbacks to be on the same interval cycle, we are now declaring a constant `INTERVAL_TIME` that we can use universally in our module.

The next thing we do is declare a `init` function:

```javascript
function init() {
  if (handleVisibilityChange) return;
  interval = setInterval(
    () => callbacks.forEach((fn) => void fn()),
    INTERVAL_TIME
  );

  handleVisibilityChange = () => {
    clearInterval(interval);
    if (document.visibilityState === "visible") {
      interval = setInterval(
        () => callbacks.forEach((fn) => void fn()),
        INTERVAL_TIME
      );
    }
  };

  document.addEventListener("visibilitychange", handleVisibilityChange);
}
```

This does a few things. First, it checks if the `handleVisibilityChange` function is truthy. If it is, we are returning early and not running the rest of the function. This ensures that we only initialize once. The rest of the function, we have already seen. We are starting the interval, declaring our event listener function, and registering it with the `visibilitychange` event listener.

There is one slight change to the interval function. Instead of calling a single function, we cycle through all the callbacks in the callback array and run those functions.

The next thing that we do is declare a cleanup function:

```javascript
function cleanUp() {
  if (callbacks.length > 0) return;

  if (handleVisibilityChange) {
    document.removeEventListener("visibilitychange", handleVisibilityChange);
  }

  handleVisibilityChange = undefined;
  clearInterval(interval);
}
```

This is doing the opposite of our `init` function. First, it checks if there are callbacks. If there are, it will return early and not perform the cleanup if there are still registered callbacks. Then after that, it removes the event listener, sets the `handleVisibilityChange` back to undefined, and clears the interval. We set the `handleVisibilityChange` back to undefined so that if the component remounts, the `init` function will rerun.

Finally, we declare our hook:

```javascript
export function usePollWhenVisible(callback) {
  useEffect(() => {
    init();
    callbacks = callbacks.concat(callback);

    return () => {
      callbacks = callbacks.filter((fn) => fn !== callback);
      cleanUp();
    };
  }, [callback]);
}
```

Now our hook takes a callback, runs `init` And adds our callback to the callback array. When it cleans up, it removes itself from the array and calls the `cleanUp` function.

We no longer need to use the callback ref since we no longer care if the effect is rerun when the callback changes. The init and cleanup already have the guards in place not to run needlessly, and the hook itself is simply adding and removing the callback from the callback array.

That's the final version, or at least final as of right now. It's fun to see how a function can change as the requirements do.
