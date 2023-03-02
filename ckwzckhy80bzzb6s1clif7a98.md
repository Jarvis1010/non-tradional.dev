---
title: "Converting A React Component to TypeScript: Lessons Learned"
datePublished: Thu May 02 2019 17:18:07 GMT+0000 (Coordinated Universal Time)
cuid: ckwzckhy80bzzb6s1clif7a98
slug: converting-a-react-component-to-typescript-lessons-learned-a8c9dd8613d3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316693312/EbHfs_Pjq.jpeg
tags: javascript, typescript

---

Photo by [solmaz hatamian](https://unsplash.com/@solmaz67?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

I have a confession. I have been a TypeScript holdout. I had my reasons, which all fell into one of two major concerns: Am I adding technical debt by adopting TypeScript and am I hurting my career by adopting TypeScript.

When I first learned about TypeScript I wasn’t confident that TypeScript had staying power. It was something primarily associated with Microsoft and Angular 2+ and didn’t feel like it had widespread adoption outside of those communities. The React community already had propTypes and had also introduced Flow, which appeared to have momentum at the time. As for my career, I was nervous about learning something that would deviate me from the main Javascript community and would force me to play catchup later on. Given all that, I decided the wait and see approach would be best.

The first sign that I was wrong about TypeScript was when Babel announced the new TypeScript transform plugin. I was also starting to hear more and more people choosing TS instead of Flow and were even migrating away from it in existing projects. My concerns regarding TypeScript’s staying power were officially gone when it was announced that the Create React App project would officially be adding TypeScript support.

It was during this time, that I had a chance to research what TypeScript really was. I learned the TypeScript was a superset on top of JavaScript and that any valid JavaScript file can be a valid TypeScript file. Unlike languages, such as CoffeeScript, TypeScript’s goal was not to add extra features or syntax to the language, beyond those that are related to types. Instead, TypeScript would defer to the ECMAScript stage and pretty much supports anything from that makes it to Stage 3. Basically, my concern that learning TypeScript would diverge from learning JavaScript were not founded.

Recently I decided I would try it out and convert an existing project to TypeScript. I decided to convert a React Component that I maintain called `react-copy-code`, which is found at [NPM](https://www.npmjs.com/package/react-copy-code). Even though the babel transform does add an easy way to start adding TypeScript files incrementally, my component was small enough that I decided to see what it was like to rely solely on the TypeScript compiler as my build process.

After working on it for half a day I was able to get it to compile, get my jest tests to run, and published to NPM using TypeScript to build instead of Babel/webpack. Now that I have done all that I thought I would share some thoughts on how things went. (As of this writing, I would not consider myself an expert on TypeScript and ask for you to comment if I am giving any incorrect information. The single best resource, that I found, for using React and Typescript is the [react-typescript-cheatsheat](https://github.com/sw-yx/react-typescript-cheatsheet) by [sw-yx](https://github.com/sw-yx). I would also recommend [React’s documentation on TypeScript](https://reactjs.org/docs/static-type-checking.html#typescript) as well as [TypeScripts documentation as well](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter))

### tsconfig.json Is Your Best Friend

Using Webpack and Babel, I had `.babelrc` and my `webpack.config.js` . With TypeScript, I only have to worry about one file: `tsconfig.json` . Your tsconfig is your source of truth on how the compiler processes your TypeScript Files. This lets you declare which files to compile, where the output should be, what the target version of ECMAScript that it needs to compile to, and other important behaviors.

There is one recommendation that I would make regarding your tsconfig, set the `noImplicitAny` property of the compiler options to false until you get your code to compile. Then change it to true after that. It should look like this:

```json
{
  "compilerOptions": {
    "noImplicitAny": false
  }
}
```

I’ll get into the `any` type more in a minute, but this basically lets you get your code to compile without declaring types in your code. This is not a long term solution but it does help reduce the noise of getting the compiler setup and working.

### Get Ready To Download Types For Your Dependencies

The code you write may not need types to compile(at least at first), but the dependencies you import does. Unless the module you are writing has types, then you need to provide those types to the typescript compiler. Luckily, there are many community-maintained types found under the “@types” namespace in npm. To download them just type in `npm install --save-dev @types/XXX` (or `yarn add -dev` ) where XXX is the name of the module you are downloading. Luckily, that is all you need to do. The compiler is smart enough to know that if the module itself doesn’t provide the types it needs, then it should look under `@types/module-name` to get it. In the very rare event that there isn’t one for the modules under “@types” then you will need to declare them yourself which you can learn more about in the [Typescript documentation](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)

### TS and TSX Cannot Be Used Interchangeably

Even though `.jsx` files do exist. It is not required to differentiate your files with JSX with the `.jsx` file type. Therefore, It’s typical to have all your React code in `.js` files even if there is JSX used in the file.

In TypeScript, it is important to differentiate them. As soon as you bring in JSX into the file, the file needs to be changed from `.ts` to `.tsx` so the compiler can treat the file appropriately. The `tsconfig.json` also needs to specify that you are using react transform for JSX, like this:

```json
{
  "compilerOptions": {
    "jsx": "react"
  }
}
```

Converting this component was not large, but it was not trivial either. Now that it’s all done, I have to say it was not as difficult as I feared it would be. It wasn’t as simple as changing `.js` to `.ts` files, but it wasn’t difficult either. The documentation is very good and the community behind TypeScript is very helpful.

As for the types, the types were also not very intrusive and fit comfortably in with JavaScript. TypeScript allows you to use an `any` type, which means that this can be ‘anything.’ This type allows you to keep JavaScript dynamically typed where it makes sense to keep it dynamic. When you set the `noImplicitAny` property to `true`, this forces you, as the component author, to be explicit when deciding to use the `any` type.

Ultimately, my experience suggests that adding TypeScript is a good idea. I intend to add it to my existing projects and use it more going forward. If you have any experiences with TypeScript that you think would be helpful for me or any other TS noob, please share. We could all benefit from your experiences.
