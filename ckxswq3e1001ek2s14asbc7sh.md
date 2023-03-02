---
title: "My TypeScript Best Practices"
datePublished: Fri May 29 2020 14:01:02 GMT+0000 (Coordinated Universal Time)
cuid: ckxswq3e1001ek2s14asbc7sh
slug: my-typescript-best-practices-845e196284aa
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316463686/vcr0JyZMy.jpeg
tags: javascript, reactjs, typescript

---

Photo by [Nils Stahl](https://unsplash.com/@nilsjakob?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

A year ago [I decided to finally try TypeScrip](https://medium.com/the-non-traditional-developer/converting-a-react-component-to-typescript-lessons-learned-a8c9dd8613d3)t. It wasn’t an easy decision, but one I am happy I finally made. If you would have asked me 3 years ago about adopting TypeScript into my go-to stack of technologies I would have fought you tooth and nail. Here I am now, and not just using it, but advocating for it. Yes, TypeScript is not the first attempt to bring type safety to the front end, Elm, ReasonML, and Flow to name a few of the most well known, but the TypeScript has risen to the top for a few important reasons.

First, TypeScript has not tried to make a “better” JavaScript. TypeScript has pretty much one goal: to make JavaScript more type-safe. Other than that, all functionality is just what is found in the ECMAScript spec. Sure, TypeScript is represented on the TC39 committee, the committee tasked to shape the growth of JavaScript, but that’s the point. It’s trying to work within the community to improve JavaScript itself and is not just going rogue and doing its own thing.

Second, Typescript has opted to allow type inconsistency, if the consistency would conflict with how JavaScript works. For example, If I call the `pop` method on an empty array, I will get `undefined` even though my array is not typed as `undefined.` This allows for consistent code and interop between our `.js` files and our `.ts` files.

The third, and I think the biggest reason it has overtaken all other attempts, is that TypeScript has embraced the community and community-driven types. This has allowed important libraries that were not written with TypeScript type definitions to easily be brought into a TypeScript code base with no friction. This also means that library authors don’t have to embrace Typescript for you to use the code in your typescript project.

Now that I have been using TypeScript for the last year, there are lessons that I have learned that have made my adoption and use of typescript much easier.

### Don’t Use TypeScript for Quick or Experimental Code

As awesome as TypeScript is at helping you write good production code, it sucks when you are trying to experiment or make a quick proof of concept. Having a dynamic language is great to just work through solutions and get something working. Adding types during these moments just slows down the creative process and, honestly is just overkill for what is probably ‘throw-away’ code anyway. If you decide you want to keep it, simply change your file into a TypeScript file and let the compiler help you refactor it with types.

### Use `.tsx` For Everything in a React Project

In a non-TypeScript project, you can use `.js` and `.jsx` and it doesn’t matter. Because of that, I never created files with a JSX extension. I never saw the benefit of trying to use a different file extension. I learned rather quickly that it was not the same with TypeScript. When you use the `.tsx` extension, the compiler knows that there is JSX in this file and to a) use those types in the file and b) look out for JSX syntax when parsing the file.

At first, it was one of those things where for the longest time I couldn’t figure out why my code wouldn’t compile, especially when the file started without JSX, but later added it. Through experimentation, I realized that you don’t have to have JSX in a `.tsx` file and it can be used just like a regular `.ts` file. So now I make all my files with the `.tsx` extension in a React project.

### Community Types Are Not Without Flaws

First of all, I am grateful beyond measure that the Definitely Typed community exists. Integrating a module that wasn’t written in typescript in a TypeScript project is do-able without them, but much more seamless because of this awesome community of maintainers. What I am about to say is not a complaint, more of an acknowledgment of the realities.

Depending on the package, the type definitions can be wrong or out of date, or don’t exist. This can happen for various reasons. One reason this happens is that TypeScript or the library makes a change that causes the type definitions to be wrong or sometimes both do. If it’s an active community, they typically get fixed right away, but just like any other OSS, they are only as fast as their maintainers. There is also the reality that some smaller and lesser-known packages will not have any types at all, even though it is a perfectly good package that is well maintained.

In these cases, there are a few things to do. You can declare a module and overwrite the types, allowing you to “fix” them in your project. That said, if you know how to fix the types, you should probably try to help the community out, but submitting a PR to the Definitely Typed repo itself.

The other easy to do option is to make use of the `any` type. The `any` type tells the compiler, I know what I am doing so don’t type check this value. It’s dangerous to use it, because it is opting out of type safety and which might not be too bad if it is isolated in a just one part of your app, but it can be very frustrating if it can’t be.

### Typescript Does Not Replace Linting

Though the two technologies can overlap in what they check, Typescript and Linting have two different purposes. Linting will look through your code and point out potential errors and bad coding practices where Typescript is just for type checking. Both are great individually, but when used together, you basically have automated pair programming going on. There is even an [eslint plugin to lint your TypeScript](https://github.com/typescript-eslint/typescript-eslint)!

### Avoid Inlining Function Types

TypeScript is pretty flexible in its type system. You can pepper in types where ever you need it and let TypeScript infer the rest. For example, we can write a function like this:

```typescript
function add(a: number, b: number) {
  return a + b;
}
```

For small simple functions, this works pretty well, but most of the time we don’t write functions this simple. Inlining our types like this start to get very messy and hard to read when we have functions like this:

```typescript
const getBooks=(
   searchParams:{
     title?: string;
     publishYear?: string;
     author?: string;
   }):Promise<({
     isbn: string;
     title: string;
     publishYear: string;
     author: string[];
    })[]>{
  return fetch(api + createQuery(searchParams:{
     title?: string;
     publishYear?: string;
     author?: string;
   })).then(res=>res.ok ? res.json(): Promise.reject(res))
}
```

Even though types can be inline, more often than not, it’s better to not do that. Instead of that, you should declare your types explicitly above the function, like this:

```typescript
type SearchParams = {
  title?: string;
  publishYear?: string;
  author?: string;
};

type Book = {
  isbn: string;
  title: string;
  publishYear: string;
  author: string[];
};

type GetBooks = (s: SearchParams) => Promise<Book[]>;

const getBooks: GetBooks = (searchParams) =>
  fetch(api + createQuery(searchParams)).then((res) =>
    res.ok ? res.json() : Promise.reject(res)
  );
```

The second example takes up a similar amount of space, but the function itself is much easier to read and understand what is going on. You will still get your IntelliSense in your editor and if you still need to understand the types, the types are declared immediately above.

As the title of this post implies, these are MY best practices. These are not hard fast rules and sometimes I will break my own best practices when I need to. What I recommend is that you try this out and see if they help you as you incorporate TypeScript into your go-to technologies.
