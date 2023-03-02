---
title: "Avoiding the Ronco style of software development"
datePublished: Tue Aug 14 2018 02:17:50 GMT+0000 (Coordinated Universal Time)
cuid: ckwzcrf180c1ab6s11anq13fo
slug: avoiding-the-ronco-style-of-software-development-83aa0e5218d4
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1639077887518/h0FcP8G-T.png
tags: programming-languages

---

Growing up in the late ’80s / early 90s, I used to love to watch infomercials. Every Saturday morning, after the Saturday Morning Cartoons had finished, I would park myself in front of the TV and watch all the infomercials I could handle. I watched all kinds of products being sold. Everything from Knife sets to Pressurized washing machines to Real Estate Investment courses would be pushed on me in 30-minute time slots. There were many companies that created these infomercials, but the king of all infomercials was Ronco, founded by Ron Popeil. I watched so many of his infomercials, I felt I had a personal relationship with Ron.

Watching these infomercials, I noticed a pattern that almost all infomercials followed. Most of these products were only good at one, maybe two things. Knowing that people wouldn’t pay twice as much for products they already owned(plus S&H), they would typically claim that, “This will be THE LAST {insert product} YOU WILL EVER HAVE TO BUY!” The hosts of the infomercial would proceed to show that you can use this one product for not just one thing, but MANY other things that, in all reality, you will probably never use that product for. Despite their claims, their product just didn’t do as good of a job on those other tasks as other products that were designed to tackle that specific problem.

Even though we all feel like we are immune from this type of salesmanship, there is a reason almost every infomercial followed this pattern: It worked! We all want that silver bullet that solves all our problems and makes us rich in the process. Who wouldn’t want just one knife in their drawer instead of a whole knife set or a paint roller that can paint the whole wall, including the edges, without needing painter's tape or paint buckets?

This human flaw infects us all and not just as consumers. Take the recent hype around React’s new context API. When I first heard about it, it was talked about as the “Redux Killer” and “The last state management tool we will ever need to use.” Debates on which is better to use have since flooded the interwebs.

Recently on Twitter, Dan Abramov brought us back to reality on this situation. Commenting about a recent blog post about Redux vs The React Context API, Dan shared this:

%[https://twitter.com/dan_abramov/status/1021850499618955272]

This simple tweet reminded us that it’s not one or the other. We have many solutions to solve our problems. Redux might be the right answer, but it might not be. Context might solve all our problems, but it probably won't. Using composition, as Dan patterned above, might solve our problems and it might not. And so on and so forth for all the other solutions that do or will exist to solve our problems.

The thing is, we don’t have to choose just one. The conversation shouldn’t be, which is better or which one solution should I use. The conversation should be, which of the solutions that exist are best for my particular situation and the answer very likely will be two or more of them in combination. Maybe we have a component that is connected to redux, but then use composition to pass that state down from there. Maybe we use Context to keep track of our string translations, but use redux for all other state management. Maybe we use all three, or maybe we use none of them and use some other solution.

The point is that we need to avoid the desire to have a single solution to rule them all. Having multiple solutions is a good thing. Just like we wouldn’t want just a butter knife or just a cleaver, we don’t want just Redux or just Context. By having more options, we have more freedom to solve our problems in a way that works best for a particular need.