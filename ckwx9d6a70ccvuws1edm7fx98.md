---
title: "How to Organize Your React App"
datePublished: Thu May 23 2019 09:11:00 GMT+0000 (Coordinated Universal Time)
cuid: ckwx9d6a70ccvuws1edm7fx98
slug: how-to-organize-your-react-app-807c70d9a83e
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316742668/YoDDD934g.jpeg
tags: javascript, reactjs

---

Photo by [Andrew Seaman](https://unsplash.com/@amseaman?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

One of the best things to be added to JavaScript is the module system. Life prior to modules was not easy. Nowadays, we are able to modularize our code into separate files encouraging good programming principles such as D.R.Y. (Don’t Repeat Yourself), code reuse, and code abstraction.

But it’s not all rosy. With the ability to modularize our code into separate files, we now have a new challenge: “Organizing our files.” Finding the right balance of code separation can be difficult, and how you do it can be very dependent upon your App and what technologies you are using. This is definitely true in React apps.

How you organize your code is very personal and I am not about to try to say that there is one way to do it. That said there are a couple of principles that you can use to help keep your code organized as it grows.

### Don’t Rush to Make New Files

Sophie Alpert recently summarized it will in this tweet:

%[https://twitter.com/sophiebits/status/1108855125135626241]

Sometimes, we get so excited about separating our code into separate files, that we don’t consider what it means to maintain them. Let's take this code as an example:

```javascript
const App = () => {
  return (
    <Layout>
      <nav>{/* ...Nav Bar*/}</nav>
      <Split>
        <main>{/* ...Main section*/}</main>
        <aside>{/* ...side section*/}</aside>
      </Split>
    </Layout>
  );
};
```

In our very simplified example above, the code doesn’t take many lines of code, but as it grows it very easy to imagine how difficult it can be to manage all that in one component. So we decide that our nav, main, and aside sections can be split into their own components. It can be very tempting to put them in separate files, but doing that can make maintaining this component difficult. Before separating them into their own files, I would recommend doing something like this:

```javascript
const App = () => {
  return (
    <Layout>
      <Nav />
      <Split>
        <Main />
        <SideBar />
      </Split>
    </Layout>
  );
};

function SideBar() {
  return <aside>{/* ...side section*/}</aside>;
}

function Main() {
  return <main>{/* ...Main section*/}</main>;
}

function Nav() {
  return <nav>{/* ...Nav Bar*/}</nav>;
}
```

This helps you keep the benefit of code abstraction while keeping common code close together. As you code, you may find the need to pull them into separate files, for example, the Nav section might be needed on other pages. At that point, then you should pull that component into its own file.

### Don’t Rush To Make Directories

Both files and directories are great tools for organizing our code, but the two are used for different purposes. Files help us group related code and directories help us group related files. What can often end up happening is that we end up creating file folders with a single `index.js` under the fear that we may need to add related files and we don’t want to refactor our code base later. Luckily, we can have our cake and eat it too. How? Let me show you.

When you import from a file you don’t have to put `.js` on the end so the top of your file could look like this:

```javascript
//file name is Nav.js
import Nav from "./Nav";
```

This is no big deal, I’m sure you have done this all the time or your code editor has done it for you without you thinking much about it. What is really cool about this is that let's say you want to factor out some code in a separate file, but you still want it grouped together, i.e. you want to group it in a directory. This is done very easily. All you need to do is create a directory with the same name as the component, move the file into the directory and rename it `index.js` . You don’t have to do anything to your import statements because import is smart enough to use the `index.js` file when you import from a directory.

The biggest take away from this is don’t rush to solve problems you don’t have. Start simple, keep it simple, and let complexity happen naturally. Your file will be much easier to maintain if you do.
