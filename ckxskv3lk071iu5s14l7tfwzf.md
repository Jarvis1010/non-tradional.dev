---
title: "Beginners Guide to Tagged Template Literals in JavaScript"
datePublished: Fri Jul 26 2019 21:01:53 GMT+0000 (Coordinated Universal Time)
cuid: ckxskv3lk071iu5s14l7tfwzf
slug: beginners-guide-to-tagged-template-literals-in-javascript-202d1805e862
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316543682/KWV0UK9sm.jpeg
tags: javascript

---

Photo by [Mockaroon](https://unsplash.com/@mockaroon?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Building up strings is an extremely common thing to do in JavaScript. Unfortunately, it hasn’t always been easy to do it. Dealing with expressions and variables that need to be injected into a string can be quite a pain to deal with. Then add in line breaks and you have an absolute mess to try to debug. Here is an example of how bad this could get:

```javascript
const price = 24.99;
const payments = 3;
const salesPitch =
  "Only " + payments + " easy payments\\n" + "of $" + price + "!";
```

But then the JavaScript gods blessed us with Template Literals. No longer did we need to manually concatenate strings. Nor did we have to manually add line breaks. Simply by using backticks (also known as the grave accent ), we can use `${}` to inject expressions directly into a string. What makes them even better is that Template Literals honor line breaks and therefore we no longer need to manually add them in.

The example above can now easily be rewritten like this:

```javascript
const price = 24.99;
const payments = 3;

const salesPitch = `Only ${payments} easy payments  
of $${price}!`;
```

If this was all we got, I would be super happy! But JavaScript pulled a Steve Job’s and said there was “One more thing” and gave us Tagged Template Literals. And now our world will forever be changed.

### What Are Tagged Template Literals

Simply put, tags are functions that perform custom parsing of a template literal. A Template literal is “tagged” when you use one of these custom parsers. A popular library that uses this feature is styled-components. This library builds “styled components” by parsing template strings with their custom tags. Here is a simple example of how to style a component:

```javascript
const Button styled.button`
 padding: 16px;
`
```

You can see it’s like calling a function but instead of using parentheses and passing in parameters like we normally would, we just tag on the template literal and the function knows how to handle it.

To show you how to build your own tags, let's build a tag function that will change integers into roman numerals. Lets call this function romeNumerize.

```javascript
function romeNumerize() {}

romeNumerize`I have ${2} cats.`;
//Output should be "I have II cats."
```

The first thing to remember is that the first argument of our function will be an array of strings delimited by the interpolations in the template. So if you pass in no interpolations, like this:

```javascript
romeNumerize`I have a cat.`;
```

Our function will get an array with a single item: `["I have a cat"]`

But if we pass in an interpolation, like so:

```javascript
romeNumerize`I have ${2} cats.
```

You will get an array of 2 strings like this: `["I have ", " cats."]`

The obvious next question is how do I get access to the interpolations? Those are available as the 2+ arguments. All the interpolations are the remaining arguments in the order they came in so we could write our tag function like this:

```javascript
function romeNumerize(strings, num) {
  return strings[0] + toRoman(num) + strings[1];
}
```

This is pretty naive to assume we are only going to get one interpolation. So a better way to handle this would be to use the rest parameters to bundle the remaining args into a single array like this:

```javascript
function romeNumerize(strings, ...nums) {
  //  ....
}
```

Now, all we need to do is “zip” the arrays together while also converting each interpolation into a Roman numeral. To help us do this, we will use one important fact regarding the relationship between the strings and the interpolations. the strings array will ALWAYS be one more than the length of the interpolations array. Even if the Interpolation is at the beginning or end of the template. So using this information our new completed tag function can be written like this:

```javascript
function romeNumerize(strings, ...nums) {
  const zippedStrings = strings.reduce((acc, cur, i) => {
    let arrToConcat =
      i === strings.length - 1 ? [cur] : [cur, toRoman(nums[i])];

    return [...acc, ...arrToConcat];
  }, []);

  return zippedStrings.join("");
}
```

In the above function, we are reducing the two arrays into one array with the final index of the string is concatenated on by itself. Obviously, if this was a real-world function we would also do some checks to ensure that each interpolation was an actual integer, but this gives you an idea of how one could go about writing a tag function.

The cool thing is that you don’t have return a string from these functions. One could simply pull in the template parts and return something else entirely. This is what styled-components is doing. It takes in the template, processes it and then returns a React Component.

One may not often find a use case for writing your own tag functions, but it is important to understand what is going. As you practice you will gain a better understanding of what is happening under the hood when we use a library like styled-components.
