---
title: "Stop Writing Reusable React Components"
datePublished: Tue Jan 14 2020 21:57:14 GMT+0000 (Coordinated Universal Time)
cuid: ckwtmjc9s0eevsms1beea5pbg
slug: stop-writing-reusable-react-components-bd649cba2700
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316571752/yfNQq0kgI.jpeg
tags: javascript, reactjs, programming-tips

---

Photo by [Ryan Quintal](https://unsplash.com/@ryanquintal?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the best things to happen to modern JavaScript Frameworks is the mental shift to components. Components change the way we think about our app. It allows us to break our App down to individual parts and test them in isolation, giving us the confidence to compose them together to make more complex components and apps. When I first started learning React, and about component-based frameworks, I was told all these things about components and how powerful they were, but the thing that always seemed to be emphasized the most was one thing: Code Reuse.

I was taught how React helped you write more DRY code (If you don’t already know DRY stands for Don’t Repeat Yourself) by allowing you to abstract the repeating code into a single component. The first class I took on React we did an exercise looking at a page and thinking about how to break this page up into components. Look, there is a Header component and there is a Hero section, I think we can make that a component. Check out that dropdown and accordions, those are components.

It was a good exercise to help break away from patterns that I had learned up to this point, but it started me down an incorrect way of looking at components. When I finally sat down to build my first non-trivial app in React, I was building a new landing page from scratch. I looked at the page I was trying to build and started drawing boxes mentally around different areas. I made a components folder and then put folders in each one for each component: Header, Hero, TileGroup, Tile, NavBar, and List.

Then I got to work making them dry and reusable as I could. Let’s take my Hero Component for example. The Hero Component was simply a `h1` and a `span` as a subtitle under it inside a div that was centered vertically and horizontally with a background image.

How to make it reusable? First, I made the values in the `h1` and the `span` dynamic by having them be passed into by props. But if you let the values be passed in by props, what do you do if “they” don’t pass in the props. What about the inner wrapper that centered the text. I need to expose the ref, className, and style props just in case “they” need it. I also need to expose those same properties on the outer header wrapper. Now let's unit test all these scenarios and write stories for them in storybook.

After following a very similar pattern and feeling good about myself, I started wiring up my page. I quickly learned one thing right away that threw me for a loop: I hadn’t written any of my components based around data. I had data needs that needed to live at different parts of the app and by building all my components before I understood how the data should flow in the app, I had locked myself into patterns that made my app difficult to wire up.

The second thing I learned much later on after I had long since left that project. The mysterious “they” that I kept writing code for never happened. Many of those “reusable” components were never reused in the app and many, including my Hero component, never even needed dynamic data. I would argue that many of these components complicated the code more by trying to build these DRY reusable abstractions. To steal a line from Chantastic over at React Podcast, my code was “so DRY it chaffed.”

Why do we do this? MPJ over at [fun fun function](https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q) [talked about this same thing recently](https://www.youtube.com/watch?v=89yJCtQZcuc) and suggested that we humans tend to write code like this as a form of procrastination. Delaying solving the hard problems we have now, by solving the hypothetical problems of the future. It’s an interesting take on this subject that I generally agree with him on.

I also agree with another point of view, recently put forward by Dan Abramov over at his blog, [Overreacted.io](https://overreacted.io/goodbye-clean-code/). Dan talks about lacking confidence in ourselves, attaching our self and professional worth “to something that can be measured. A set of strict lint rules, a naming schema, a file structure, a lack of duplication”.

Ultimately it comes down to our inner human flaws of anxiety and fear. Fear of facing the real problem in front of us that we don’t (yet) know how to solve and/or the fear and anxiety that comes with feeling like an imposter, so we overcompensate by attributing our worth to how “clever” we can write our code.

What can we do now to prevent this? The first thing is being honestly aware of it. Regularly ask yourself am I solving for a real problem or a hypothetical problem? Am I avoiding the real problem? Is this code the right abstraction i.e. is code reuse the problem I have right now? If you are honestly answering yes to these questions, they go abstract away. Otherwise, leave it alone and wait until the answer is yes. You will be much happier in the long run.