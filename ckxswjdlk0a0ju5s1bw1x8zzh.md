---
title: "Lesson’s Learned From Pre-CSS Layout"
datePublished: Mon Apr 12 2021 14:03:27 GMT+0000 (Coordinated Universal Time)
cuid: ckxswjdlk0a0ju5s1bw1x8zzh
slug: lessons-learned-from-pre-css-layout-798c1be8d6c5
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316470614/A2eP_jsxF.png
tags: css

---

Netscape’s website in 1998 (source: [https://www.webdesignmuseum.org/exhibitions/web-design-in-the-90s/netscape-1998](https://www.webdesignmuseum.org/exhibitions/web-design-in-the-90s/netscape-1998))

At the beginning of the web, there was no web layout. The ability to have a visual design wasn’t even the problem that was being solved. The founders created HTML’s with a single purpose: to send and receive content that was agnostic of the operating systems sending and receiving them. Styling that content was not even on the radar.

As the web began to be adopted for commercial use, this lack of styling left a significant vacuum that needed to be filled. Eventually, CSS would fill that void, but the road to get there brought some innovative solutions. Solutions that, despite how hacky they were, have had a lasting impact on how we layout the web today.

### **Spacer Gifs**

One of the most important aspects of laying out a web page is creating consistent negative space between our elements. One common trick that early webmasters developed was to use spacer GIFs.

Spacer GIFs are what the name suggests. They are transparent images that take up space, giving the illusion of negative space. One used an `img` tag whose source was a 1px by 1px transparent GIF to implement a spacer GIF. You could then use the `hspace` and `vspace` attributes (which are now deprecated) to add horizontal or vertical space around that image, like this:

One could strategically place spacer GIFs in such a way as to create gutters in between the elements, like the example above.

Though this solution resolved the need to create consistent gutters between our elements, it does this by adding markup to create negative space. Not only does this add unnecessary page load, but it also distracts from what is going on in the page and creates a less than ideal environment for maintaining the page.

In CSS, it is much easier to add negative space without extra markup. Margin has been supported since the earliest versions of the spec. In CSS Grid, we also have the `gap` property that allows parent elements to create consistent gaps between their direct children. This property is also available for flexbox in almost all the major browsers and supported by all browsers by the end of 2021.

### **Presentational Tags**

Without a standard for styling the HTML elements, browsers filled that vacuum with a solution of their own. They extended the HTML standard, often called HTML+, which included presentational tags that added the ability to apply styling to the content. Many of these tags were adopted in HTML 3 but were later deprecated in favor of CSS.

From a layout perspective, there is one notable presentational tag: the `center` tag. The `center` tag does what it says: it centers the content inside of it. For example:

These presentational tags had their advantages. They did make it easy to see how the content would be styled but had many other disadvantages. Since all styling had to be done inline, applying styles on multiple pages using a common styling theme quickly became cumbersome.

The other disadvantage was that these presentational tags had no semantic meaning when it came to accessibility. Sure, the content may be centered and bolded, giving meaning to those with sight, but how do you translate that to screen readers, or should you? With the adoption of CSS, we now let CSS be in charge of the page’s visual structure and let HTML focus on its semantic structure.

In modern frameworks, we can now build layout components that maintain semantic meaning but have built-in layout opinions that we can compose together like these presentational tags. (Time for a shameless plug my open-source library: [Bedrock Layout Primitives](https://bedrock-layout.dev))

### **Framesets and Frames**

Another set of tags used for visual layout were the `frameset` and `frame` tags. `frameset` tags were used in place of the `body` tag of an HTML page. They allowed you to compose one or more `frame` tags together to create rows and columns of content. Each `frame` tag could render independent HTML pages, which would be laid out according to the `frameset` parent tag.

Here is a straightforward example of a page using the `frameset` and `frame`tags:

`frameset` tags had many advantages. Not only did it allow you to compose layouts by nesting `frameset` tags inside of other frames, but it also allowed each part of a page to be loaded and rendered independently. One could focus on updating sidebar content without touching your page’s header, footer, or main content.

Unfortunately, they created just as many problems as they solved. For one, because your page was loading multiple HTML pages, accessibility and search engine optimization were non-existent with frames. It also created situations where someone might accidentally directly load the HTML intended to be in a frame, such as the footer.html, making a horrible user experience without the rest of the intended content.

Besides the composable layout benefits that we talked about earlier in the presentational tags, modern component libraries allow us to achieve some of the same benefits of frames without the downsides. Components in charge of different parts of the web can be created and maintained independently of each other and can be updated on the client’s browsers independently.

Though it can be easy to look down at the layout solutions that pre-date the universal adoption of CSS, a lot was learned and helped inform how we build layouts on the web today.