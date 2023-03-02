---
title: "The Only Four Git Commands You Need (To Get Started)"
datePublished: Thu Sep 03 2020 14:01:04 GMT+0000 (Coordinated Universal Time)
cuid: ckxpqv2lm038o1ms19g80cire
slug: the-only-four-git-commands-you-need-to-get-started-89e88855d348
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316569069/D462Anvv2.jpeg
tags: git, codenewbies

---

Photo by [sarandy westfall](https://unsplash.com/@sarandywestfall_photo?utm_source=medium&utm_medium=referral) on[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Source control is one of the most important tools you can learn when becoming a developer. Source control is used for many things. It’s used to keep a history of your code. It is used as a collaboration tool for team members working on the same code base. More and more, it has become the standard way to trigger automated code integration and deployment. Though there are several source control tools out there, Git has become the defacto standard.

Despite this, Git can be intimidating, especially for new developers. Luckily, Just like how you can get by very well only learning the most common words in a spoken language, you can be very successful at Git with just four commands.

### How Does Git Work

The best way to understand git is to think of a stack of photos. Each photo represents a moment in history. The top photo is the most recent snapshot and to go look at previous snapshots, you have to take off the photos to get to it.

Git, more or less, works the same way. It is a series of snapshots called `commits` that show what your code looked like at any given point. You can use Git to travel through time and see what your code looked like at any given snapshot and even compare any two snapshots together and see how they differ, which is called getting a `dif` of the two commits.

### How Do You Start Using Git

First of all, you need to install git on your computer. If you have a Mac, I would recommend installing XCode from the app store. XCode will install git automagically for you. For Windows machines, I would recommend following the instructions over at [https://gitforwindows.org/](https://gitforwindows.org/)

After that, you need to have a git project called a repository or repo for short. You can manually turn any directory into a git repo, but when you are just starting with git I would recommend using [Github’s new repository wizard](https://docs.github.com/en/github/getting-started-with-github/create-a-repo). They make it simple to get started and offer many common start files you will want to have for certain types of projects. What it will do is create the repository and then give you the commands that you can paste into your terminal to clone the repository on your local machine. From there you are ready to learn the four most important commands

### The Four Commands

So now that we are in a git repo and we have made some code changes that we want to take a snapshot of, how do we do that? The first thing we need to do is stage our files. Staging is nothing more than deciding which changes will go in this snapshot. I like to think of staging kind of like taking a picture and you are going to decide who and what is in the shot before you take the picture itself.

To stage the changes, we use the first command-line command: `git add.` which simply stages all the changes. If you read my [Command-Line Basics post](https://medium.com/the-non-traditional-developer/command-line-basics-for-new-javascript-developers-ec80d2867503), you will remember that whenever you use the period,`.`, at the command-line, it means the current directory. So typing in `git add.` means add all changes in the current directory to the stage. If you were looking to do just some of the changes, you could instead type `git add <file.name>` and it will only stage the single file. This is a cool thing to learn, but when you are starting, I feel it’s best to just use `git add.` and that way you don’t miss any changes.

Now the files are staged, but we still don’t have a snapshot of our code. To do that we need to run our next command: `git commit -m "<message>".` This command will take a snapshot of all the file changes and attach to it a message. That is what the `-m` flag is for, to add a message to the commit. There are several opinions out there on what should go in your message, but I say don’t worry about those. Each team you work on will have various levels of opinions on what you should do, but in your projects, you should do whatever you want.

At this point, we are good. We have staged our changes and put those changes into a single commit. We can keep working, staging, and then committing our changes over and over again. It’s all good and fun to write code on our machine, but the code that is only on our machine is not very valuable. This is where our next two commands come in handy.

What you will want to do is `push` your changes from the local repository to the remote repository. If you started your repository on GitHub and cloned it down to your machine, then you don’t have to set up the remote repository. Otherwise, you will have to [set that up yourself](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes), which is honestly very easy to do.

To push your changes up to your remote repository, you simply type: `git push` at the command prompt and git will push those changes up for you. Then you can go to Github and see all those changes will be reflected in the code there.

What is cool is that many of the deployment pipelines, like Netlify or Azure, can listen for changes on your remote repo and automatically build and deploy your code for you making deploying your code as simple as typing `git push`

The final command-line you need to get started is the inverse of the previous one. Often we will want to work on the same code base on multiple machines. If we make changes on machine A and push those changes up to the remote repository, machine B doesn’t automatically get those changes. For the other machine to get those changes on to its local repo you need to type `git pull` at the command prompt. This command does exactly what it says it does, it pulls the changes from the remote repository back to your local machine.

That’s it! With those four commands, you can do most of the heavy lifting that you need to maintain a codebase. Once you get comfortable with those 4 commands, you can look at learning about branching, rebasing, and merging but for now those are not important. I will leave you with one piece of advice that has served me well: “Commit early, commit often.” It is an easier task to look back on smaller commits than large ones. There is never a bad time to commit.
