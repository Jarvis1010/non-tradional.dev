---
title: "Modern JavaScript’s Prior Art: jQuery"
datePublished: Tue Feb 04 2020 21:21:47 GMT+0000 (Coordinated Universal Time)
cuid: ckxpqrnb801nfe3s1et3c8b39
slug: modern-javascripts-prior-art-jquery-c57b83aff270
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316578024/zX6U74tfw.jpeg
tags: jquery, javascript

---

Photo by [Birmingham Museums Trust](https://unsplash.com/@birminghammuseumstrust?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

JavaScript has come along way from the first version created by Brendan Eich decades ago. Modern JavaScript is feature-rich and allows you to write some pretty powerful code in a clean readable way. Now that the TC39 committee has a more agile process to add features regularly to the ECMAScript spec, these features will continue to roll out regularly.

Given all these new features and APIs, it can be very easy to forget that many of the awesome features that we enjoy used to only be available through other libraries or just implementing it ourselves. So I thought I would share some of Modern JavaScript’s Prior Art, or in other words, what influenced it to become what it is today. In this post, I will be focusing on jQuery.

It is easy for us to crap on jQuery now, but jQuery was and still is an amazing tool that made JavaScript so much simpler to build apps with. It is still used in a majority of the web pages that exist and is still in active development. That can be attributed to how much jQuery changed the way we write JavaScript for the better! There are many things people can point to that has influenced modern JavaScript, but the two primary ways that I feel it did that, were to simplify Asynchronous Data calls(AJAX) and through a much more intuitive DOM tree traversal tool.

### DOM Selectors

Before jQuery, one primarily used a few static methods on the `document` object: `getElementsByTagName, getElementsByClassName, or getElementById` . Besides the easy to miss bug of forgetting that the `getElementById` method is a singular word “Element,” while the other methods use the plural form “Elements”, It was also very limiting. If we wanted all the spans inside a paragraph tag, for example, we would have to write something like this:

```javascript
var spanList = [];
var paragraphs = document.getElementsByTagName("p");

for (var i = 0; i <= paragraphs.length; i++) {
  var spans = paragraphs[i].getElementsByTagName("span");
  for (var j = 0; j <= spans.length; j++) {
    spanList.push(spans[j]);
  }
}
```

One had to first get all the paragraphs and then get all the spans in the paragraphs and push them into a master list. Only then could you start working with them.

jQuery added a much easier API by allowing you to use the same syntax as CSS selectors to get elements. So something like this `getElementsByClassName("btn")` could now be written like this: `$(".btn")`. Also, we could use the already familiar selector syntax to get the exact elements we wanted with a single call. Using the above example you could now write it like this:`var spanList = $("p span").` Wow! Eight lines of code and a nested for loop has been changed to a single line of code. That is some amazing power!

In modern browsers (IE 9+) we have two new static methods on the `document` object: `querySelector` and `querySelectorAll.` These two methods allow us to use the same CSS selector syntax as jQuery. If one only wanted the first node that matches that selector, then you use the `querySelector` method. If you would like all the matches, then you use `querySelectorAll.` So now we can take that jQuery code and convert it back to vanilla JavaScript like this: `let spanList = document.querySelectorAll("p span").`

It is important to note, that the querySelector methods are not a drop-in replacement of jQuery. jQuery also added in some method standardization to account for browser differences. It had some simple ways that one added event handlers and appended DOM nodes that worked across all browsers. Current browsers tend to keep feature parity with each other and, for the most part, are consistent in their implementations. So if you were moving away from jQuery, you will have to change syntax from the jQuery API to the standard DOM API.

### AJAX

Asynchronous JavaScript and XML, or AJAX for short, was a paradigm-shifting way of writing web pages. Before this, webpages received by clients were static documents of HTML, CSS, and some JS for limited logic. AJAX allowed for dynamic content asynchronously sent and received. In short, a browser could load a web page, the data needed for the site could then be requested and JavaScript could then parse the data and dynamically update the DOM on the client. Initially, XML was the data form of choice (hence the X in AJAX), but JSON has taken over as a more standard way of sending data to and from the server.

Unfortunately, the way this was initially implemented in JavaScript was not super easy to use. The earliest forms of AJAX were implemented using the XMLHttpRequest Object. Here is an example that I’ve [borrowed from MDN](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest):

```javascript
function reqListener() {
  console.log(this.responseText);
}
```

```javascript
var oReq = new XMLHttpRequest();
oReq.addEventListener("load", reqListener);
oReq.open("GET", "http://www.example.org/example.txt");
oReq.send();
```

As you can see, the process is to instantiate a new XMLHttpRequest object and then add event listeners. In the above example, it is listening to the `load` event but there were other events such as the `error` and `abort` events that you could add event listeners for. Your event listeners also had to correctly work with `this` which is easy to screw up in JavaScript, no matter how good at it you are.

With even listeners in place, you can now open a request, by first specifying what HTTP verb you were going to use and what URL to use it from. At this point, one may think we are done since I have now “opened” the connection. You would be wrong because you still need to call the send method to get the ball rolling. As the need for your request grows, so does the complexity of the object.

jQuery simplified all of this. They added a simple `ajax` method that took in a configuration object that would include a call back on success. This configuration object was easier to use and reason about than the XMLHttpRequest object. jQuery also added some alias methods that implemented common defaults. One of those was the `get` method that simply took a URL and a call back to run when the data was loaded. The above code could be rewritten like this:

```javascript
$.get("http://www.example.org/example.txt", function (data) {
  console.log(data);
});
```

No more Opening a connection then manually calling send. No more worrying about `this` and other complicated features. One could simply “get” the data you needed. If you needed, the ajax method allowed for a more configurable request but was still much simpler to use than the XMLHttpRequest object.

Browsers have now added a new API called `fetch` that is now much easier to use. Fetch uses the new Promises API introduced in ES6 to make a much simpler API for sending and receiving data. Even though it is based on promises and jQuery is not, it is easy to see the influence that jQuery has had on the API.

Fetch only need one argument passed to it, the URL and it will do a GET request by default. If you need more than a basic GET request, the second argument takes a configuration object that allows you to change the verb, add headers and a body, and other important things to allow you to make the right kind of call that you need.

As you can see, modern JavaScript has come along way and it has taken the community to get us there. As libraries have been built to help make it easier to solve those problems, JavaScript has been able to be updated to implement the best features from these libraries so we can all benefit.
