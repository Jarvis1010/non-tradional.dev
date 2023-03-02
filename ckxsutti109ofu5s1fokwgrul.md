---
title: "My First Impression of Tailwind.css"
datePublished: Thu Aug 05 2021 08:26:33 GMT+0000 (Coordinated Universal Time)
cuid: ckxsutti109ofu5s1fokwgrul
slug: my-first-impression-of-tailwind-css-b0541d5c553c
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316517700/4Jsn80Kz9.jpeg
tags: css, tailwind-css

---

Photo by [Khamkéo Vilaysing](https://unsplash.com/@mahkeo?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Recently, [on my twitch channel, I integrated Tailwind.css and Bedrock Layouts](https://www.twitch.tv/videos/1107884602). Before that stream, I knew very little about Tailwind.css other than what can be read online. Interestingly enough, Tailwind is a very polarizing library. For example, when searching for Tailwind.css on medium, I found both [a favorable article](https://codeburst.io/six-reasons-why-you-should-start-using-tailwind-css-402292c50a70) by [Varun Pujari](https://medium.com/u/1a0c8d8509da) and [an unfavorable one](https://betterprogramming.pub/tailwind-css-is-probably-overhyped-5272e5d58d4e) by [Gerard van der Put](https://medium.com/u/9292823729fa).

Now that I have finally used it, at least a little, I thought this was a good time to write down my first impression of Tailwind.css.

### Tailwind is Not a Design System; It’s a Language

Tailwind sometimes gets grouped with other well-known design systems like Ant Design and Material UI. In fact, Tailwind doesn’t even claim to be a design system. You could call it a CSS framework, but even that seems too high level.

Tailwind’s relationship to CSS is more similar to CoffeeScript’s relationship to JavaScript. Tailwind’s utility class API makes you feel like you are writing CSS and even uses many of the same syntax and terminology. Tailwind, however, is an entirely different language that is effectively pre-compiled down to CSS.

This means that to master Tailwind, one needs to learn Tailwind’s domain-specific language. Yes, one’s knowledge of CSS can make it easy to learn, but you will have to learn a new language nonetheless.

### Would I Use Tailwind Again?

I will acknowledge that this is an opinion based on minimal use, so take it with as many grains of salt as you want. I personally would not look to use Tailwind.

First of all, as I stated above, Tailwind is more like a Style language than a framework. If I am going to learn a new language, I better get some major benefits out of it. Unfortunately, I don’t think I am.

A significant amount of the class names in tailwind don’t even have opinions attached to them. They are simply just names of the property and value you are setting. For example, the class `stroke-current` sets the property `stroke: currentColor` and `opacity-95` sets the `opacity` property to 0.95. The only thing that it saves me is having to write the CSS itself out in an actual stylesheet.

I feel that the class names that enforce an opinion could be just as effective by setting their values as custom properties in the root.

Secondly, many times, it’s been pointed out that the shortcomings of utility first frameworks, like Tailwind, are that you can end up with many class names on a single element. This is true, but even worse than this, the meaning of the class names becomes lost.

Here is an example right of their landing page:

```html
<div class="w-64 h-3 bg-gradient-to-br from-fuchsia-500 to-purple-600"></div>
```

This `div` has no meaning to me. I admit that I’m not super familiar with the class names, but even if I was familiar with them, I am confident I wouldn’t be able to see at a glance what the purpose of this `div` is.

The final reason, and the most important to me, is that Tailwind breaks the principles of Encapsulated CSS. [I have written about the rules of Encapsulated CSS before,](https://non-traditional.dev/encapsulated-css-the-key-to-composable-layouts-94f11c177cc1) but to summarize:

1.  Elements don’t set their own layout properties, namely properties that change the size, position, or margin.
2.  Elements set their own styles and lay out their children.

Tailwind encourages setting layout properties directly on the children. I couldn’t find a way to set layout properties on an element’s children using Tailwind’s utility classes. For me, this is a deal-breaker.

I understand that Tailwind is EXTREMELY popular, and more power to you if you like it. Any tool that you feel helps you ship features to customers quickly and efficiently is a good tool, which Tailwind obviously is. For me, it’s a no-go for the reasons stated above.

As Lavar Burton said at the end of every episode of Reading Rainbow, “You don’t have to take my word for it.” Try it yourself and make your own decision.
