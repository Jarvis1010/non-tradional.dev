---
title: "Easy Accessibility Fixes That I Wish I Knew as a New Developer"
datePublished: Mon Sep 14 2020 14:01:01 GMT+0000 (Coordinated Universal Time)
cuid: ckwok06a90fnb2ds1a31u1m6d
slug: easy-accessibility-fixes-that-i-wish-i-knew-as-a-new-developer-fd5c11b08b4a
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316784384/JQuPaVmWy.jpeg
tags: web-development, accessibility, html5

---

Photo by [Yomex Owo](https://unsplash.com/@yomex4life?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Not long after I received my first dev job, I started learning about the importance of web accessibility. It isn’t that I didn’t know about it or think it was important, I have a blind cousin and a father with multiple disabilities including hand tremors caused by Multiple Sclerosis. It’s not like I didn’t hear about it when I was learning web development either. Honestly, it was never emphasized in any of the resources I found to learn web development and I didn’t know enough to go looking for any resources either.

So there I was, a junior dev full of imposter syndrome, discovering that I had another huge hole in my knowledge base, and if I admitted that to anyone, then I would be revealed to be the fraud that I am. So I went on writing code, feeling guilty that I was potentially writing inaccessible code. (Never noticed that no one called me out in code reviews because they didn’t know either )

Then I went to a conference and the keynote speaker was blind and talked about how technology was not just enabling it is for him, but also how much inaccessible technology prohibits him in his day to day life. It was at this moment I made it a point that I would change ”the definition of done” and always do my best to make sure my code was accessible to the best of my knowledge.

The thing is, just like many things, the things that will get you the most bang for your buck are the small and simple things and very easy to implement. It’s just simply knowing about them so you are aware of them when you use them. Here is a list of easy accessibility fixes I wish I knew when I was starting.

### Always Use a Descriptive Page Title

Page titles are easy to set and probably one of the first HTML tags you learn. It’s also one of the most neglected tags on a page. Visually, the page title is only shown at the top of the tab. This can make it very easy to set it to something simple once on every page and then forget about it.

Screen readers, on the other hand, announce the title every time the page changes. Imagine how confusing it is when you click a link and navigate to a new page, only to have the same default page title announced every time. All one needs to do is put a descriptive page title for every page and this problem is easily fixed.

### Use Heading Tags as Landmarks And Not For Styling

Heading tags come in 6 levels, h1 — h6, and organize the flow of our page. They also provide landmarks for screen readers to easily jump to different sections based on that flow.

The problem is that they also have default styles provided by the browser and/or CSS framework we are using. This means it can be very tempting to use heading tags for their styles, even though it makes the flow of our page impossible to understand.

Here are some simple rules that can make this easier:

1.  There should only EVER be one `h1` tag on a page.
2.  Never skip a heading level. i.e. `h4` should never come after an `h2,` you should use an `h3` instead.
3.  Use CSS to style the element the way you want. If the heading is too large or too small then change the styles with CSS. If you want something big and bold, but it is not a heading landmark, then don’t use a heading tag. You probably need to use either a strong tag or emp tag and then add the styling you need to make it look the way you want.

Now there are times when, visually, no heading is needed. Lists of blog posts or things like that are easily understood just by looking at it. One doesn’t need to add a heading for your sighted users in that case, but you still need a heading there. This is because, once you remove all visual cues, it no longer makes sense of what is going on there. In these cases, I would recommend using the visually hidden hack. A great example of [how to implement this hack is at ReachUI](https://reach.tech/visually-hidden/#:~:text=Provides%20text%20for%20screen%20readers,icon%20and%20ignores%20the%20text.).

### Placeholders Are Not Labels

In today’s modern webpages, space is a premium. It is typical to try to minimize the pixels our form fields use, maximizing the limited space we have. One of the ways that developers have started doing this is by using the placeholder attribute as a label.

This is, unfortunately, creates a bad experience not just for those who are dependant on screen readers, but for sighted users as well. Without a proper label, you have no context for what the form field is for. Visually the placeholder value is removed the moment you type anything, requiring you to delete the text you wrote just to see the placeholder value again. It’s a bad experience for everyone.

In addition to this, we lose out on one of the best built-in accordances that the browser gives us for free. When a label is properly associated with an input field, anytime you click or tap the label, it will bring focus to the input field it is associated with. This creates a larger surface area for finger taps and mouse clicks, which is especially helpful for those, like my father who had hand tremors, to click on the field they need to click on.

If you are looking to still maximize the space, there are well-documented tricks that allow the label to float from directly on top of the field when the field is empty and then change to directly above the field when the user starts inputting values. The placeholder values are still useful. They allow you to provide supplemental information about the form field such as examples. Just remember that you should never put in information that is important after to have after the field has been filled out.

### Every Image Tag Must Have an Alt Attribute

As you have noticed, one of the biggest things we need to remember when writing out web pages is that they are not just for visual users. We need to write our web pages in a way that benefits non-sighted users as well. So what do we do with `img` tags, that are one hundred percent “visual”. This is where the `alt` attribute comes in. The `alt` attribute allows you to provide a detailed description of what the image was supposed to show. The browser will display the value of the `alt` attribute and then show the image if it is retrieved. In the case of screen readers, only the `alt` value is used.

So what do you put in the `alt` attribute? Simply put, enough detail that if someone cannot see the image, they can get the same value. One may be tempted to say use “Image of” or “Picture of” but that is already implied from the context. Instead, just write down what important information the user would have gotten if they had seen the image.

In some circumstances, the image provides no extra value and is just to make the page more visually pleasing. This includes profile photos or images in a button that already has a label. In these cases, providing a description actually is distracting. That doesn’t mean you can get away with not using the `alt` attribute. If the `alt` attribute is not provided, the image file name is used, which will make the experience worse. Instead, you should provide a blank value like this:

```jsx
<img src="/bob_smith_100_300.jpg" alt="" />
```

This will tell the browser/screen reader to ignore this image. Once again, only do this if the image really doesn’t provide any information that needs to be conveyed to the user.

### Don’t Remove The Focus Outline

The last thing I will bring up is the focus outline. Often, when we talk about disabled users, we immediately think of blind users. But disabilities come in many shapes and levels of permanence. One of those disabilities could be those who can see just fine but cannot use a mouse for whatever reason.

For this reason, browsers provide a focus outline so that user’s who use keyboards as their primary navigation method can visually see where they are on the page. Despite this great affordance that browsers give us for free, developers and designers often remove it in their CSS, making their web page unusable for keyboard navigation.

Why does this happen? Two primary reasons why this happens. First of all, no two browsers style their focus outline the same, and second of all, Browsers also show the focus outline when you click on a focusable item if the default styling has been changed at all. This means that if you are using any CSS styling at all, which everyone is, then you are seeing these weird non-standard looking focus rings around items, even when you are not using a keyboard.

The first problem is easily solved. It’s just as easy to make your focus outline styling in CSS as it is to turn it off. Then you can standardize how it looks on all browsers.

The second issue is also pretty easy to solve as well. Currently, the CSS working group is [working on a native way to better control when the focus is visible,](https://github.com/WICG/focus-visible/blob/HEAD/explainer.md) specifically when navigating via keyboard and not the mouse. [There is a polyfill called focus-visible](https://www.npmjs.com/package/focus-visible) that you can easily add to your project now and take advantage of it.

This list is in no way exhaustive. I highly recommend you look at developer tools like google lighthouse, web aim, and axe to help you audit your web pages for accessibility issues. Install and/or activate the free screen readers that are available on your machines and learn how they work.

I’ll end with one last thought. When the American’s with Disabilities Act was passed, many cities and buildings had to add ramps and curb cuts to allow those in wheelchairs better access. The crazy thing is that despite them putting these affordances in for “disabled” individuals, everyone noticed the benefits of it. Delivery people with dollys could access buildings easier. Those pushing baby prams could more easily walk around the cities. In the same way, making the web more accessible builds a better user experience for everyone.
