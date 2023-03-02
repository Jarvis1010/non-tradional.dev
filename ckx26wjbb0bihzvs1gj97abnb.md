---
title: "Web Animations the Disney Way: Squash and Stretch"
datePublished: Thu Jan 10 2019 14:55:20 GMT+0000 (Coordinated Universal Time)
cuid: ckx26wjbb0bihzvs1gj97abnb
slug: web-animations-the-disney-way-squash-and-stretch-a1b09d99aa5c
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316619902/akDYA0Jmi.png
tags: css-animation

---

This is the first of a twelve part series where we will explore the 12 principles of animation as taught by Frank Thomas and Ollie Johnston, in their book, *The Illusion of Life: Disney Animation* and how to apply them when animating on the web.

One of the most important things about creating a user interface is that the interface should be enjoyable. If our UI feels wrong and janky, users won't want to use our application no matter how awesome it is. There are many things that go into good design and I don’t want to pretend to be an expert at any of them. That said, I can confidently say that animations are an import part of a user interface.

How do you make good animations? There are many resources and opinions out there, but I stumbled on one unexpectedly. My daughter is an up-and-coming animator. She has a [YouTube channel](https://www.youtube.com/channel/UCA9eP3nl-eMIRhpVMfTyW1A) with regular subscribers and everything. As an animator, she is learning and honing her craft and always looking to learn best practices.

One of the resources that she uses is a book called *The Illusion of Life: Disney Animation* by Frank Thomas and Ollie Johnston, two very important animators from the golden age of Disney animation. In the book, they describe how the Disney animation studio became an extremely successful studio that it was and still is.

One of the struggles they had was how to train new animators. Animation wasn’t like it is now. There weren’t college programs or YouTube tutorials that taught people how to animate. They had to hire talented people and then teach them how to animate. This led to them to create what is now known as the 12 principles of animation. These 12 principles were the result of their collective experiences in creating high-quality animation that felt real. It was a tutorial on how to give the illusion of life.

It was originally just a tool to help onboard new developers, but it has now grown to become the bible of all animation. The 12 principles are still taught today and, despite being created for 2-D animation, are still applicable in 3-D animation (though the application of the principles are not exactly the same)

This book made me think. If these principles were important in creating an enjoyable film or short, then would it also be safe to assume that these principles would apply in web animations as well.

So that is the intention of this post and future posts in this series. I will be learning and sharing with you the principles and figure out how it can and should be applied in a UI. Though I might have code examples when appropriate, the main purpose of this is to keep it high level and to start a discussion on how one would use this principle to improve the experience for our users.

The first principle of animation is called “Squash and Stretch”. This is probably one of the most important principles. Applying this principle gives the illusion of both weight and flexibility. Animators observed that only very rigid objects maintain their shape while in motion. All other items would squash and stretch during movement. The more it flexible it was, the more it would squash and stretch. Here is a short video explaining the principles:

<iframe src="https://www.youtube.com/embed/haa7n3UGyDc?feature=oembed" width="700" height="393" frameborder="0" scrolling="no"></iframe>

Principle 1: Squash and Stretch

It is important to point out that the object’s volume does not change. As it stretches longer, the object’s width becomes thinner to compensate and vice-versa. Animators do break this rule for comedic purposes, but as a rule, the volume stays the same throughout the motion.

So I decided to give it a whirl. I used React-Pose to assist in applying the animations as well as styled-components for the rest of the styling. I have two types of animation: a box that toggles in an out of frame vertically and another that slides left and right horizontally. Finally, so you had something to compare it to, I provided a similar box for each example that just applies a simple ease-in and ease-out animations. Check it out:

<iframe src="https://codesandbox.io/embed/2vw3onx6x0" width="1192" height="596" frameborder="0" scrolling="no"></iframe>

So what did you think? I took a very unscientific poll of people I know and they almost all agreed on one thing: the squash and stretch version was more enjoyable. Most couldn’t say exactly why other than squash and stretch just felt more enjoyable to look at. That’s the point of this. Animations, shouldn’t be a distraction. It should improve the overall experience and feel natural. The ease-boxes feel less organic and out of place and it detracts from the overall experience.

What do you think? How can you apply this principle in your next web animation? Please let me know. I would love to hear your thoughts and experiences.