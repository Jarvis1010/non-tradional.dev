---
title: "Your React Storybook Game Sucks"
datePublished: Tue Jul 27 2021 14:03:02 GMT+0000 (Coordinated Universal Time)
cuid: ckwxa5odl0cjguws18tpobom5
slug: your-react-storybook-game-sucks-225c7eb139ec
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316727560/LAsU0VW78.png
tags: javascript, reactjs

---

I was introduced to Storybook in 2017. Then it was under the `@kadira/storybook` repo, and I used it to help build out components in isolation of the app I was building. It made it easier to plug in the components later into the actual app.

Storybook has come a long way from those days. It is now maintained by the [team over at chromatic](https://storybook.js.org/team/). It is still built on that same foundation of building components in isolation, but it does so much more than that now. If you are not taking advantage of that, you are missing out.

I decided to compile some resources so your Storybook game won't “suck” anymore:

### Testing

Storybook has always been built on this concept of testing your components in isolation. More and more, Storybook has also tried to bridge the code you write for unit tests and the code you write for stories.

One of those ways is using snapshot testing from your stories. Typically, the code used for your stories is the same as the code for your most of your snapshot tests. It can get cumbersome to maintain duplicate code for both stories and snapshots. This is where [the snapshot plugin](https://storybook.js.org/docs/react/workflows/snapshot-testing#gatsby-focus-wrapper) comes in handy. It turns your stories into jest snapshots that get checked just like any other snapshots when you run jest.

If you’ve been in the React community for any period of time, chances are you have heard of the React Testing Library. React Testing Library has become the most reached for testing library to test React components. Unfortunately, using it with Storybook means you have to maintain two almost identical configurations and setups.

Luckily, [Yann Braga](https://medium.com/u/56ffeddc77cd) [announced the](https://medium.com/storybookjs/testing-lib-storybook-react-8c36716fab86) `[@storybook/testing-react](https://medium.com/storybookjs/testing-lib-storybook-react-8c36716fab86)` [package.](https://medium.com/storybookjs/testing-lib-storybook-react-8c36716fab86) That allows you to integrate the Storybook and React testing library together. It greatly simplifies your testing setup.

### Documentation

It’s been often said that a well-maintained testing suite doubles as documentation for your components. In Storybook, this isn’t just a figurative statement. [Storybook stories can be your literal documentation of your components.](https://storybook.js.org/docs/react/writing-docs/introduction%5C)

Storybook supports MDX-style stories that allow you to write the documentation for your components right next to the stories that implement those docs. Storybook can then be built and deployed anywhere to serve as your components documentation.

Add on top of that, [Storybook supports the ability to theme](https://storybook.js.org/docs/react/configure/theming#gatsby-focus-wrapper) your storybook any way you want. Storybook can be the official documentation site for your component library. In fact, that [is exactly what I did for Bedrock Layout Primitives.](https://bedrock-layout.dev/)

### Visual Regression Testing

Just like any code we write, components are susceptible to regression. Adding a feature or refactoring the code can break the way our component is supposed to work, which is why we have tests. Components are inherently visual. You can run and pass your snapshot tests all day long, but if they don’t pass the visual test, then they are wrong.

Unfortunately, manually checking that the code looks right every time we make a change can be tedious and error-prone. Luckily, [Storybook has provided tools](https://storybook.js.org/docs/react/workflows/visual-testing) that allow us to automate visual regression testing.

The coolest of those tools has got to be the integration of [Chromatic with Storybook.](https://www.chromatic.com) Chromatic will build your Storybook and Visually compare the changes from the previous build. If your story visually changes, it will alert you and show you exactly how the two builds are different. From there, you can decide to fix it or accept the new changes.

This is actually just a small fraction, but with these three things, you will up your Storybook game and build well documented and well-tested components.