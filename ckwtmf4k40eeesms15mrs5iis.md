---
title: "Semantic Versioning  for Dummies"
datePublished: Thu Jan 03 2019 15:48:30 GMT+0000 (Coordinated Universal Time)
cuid: ckwtmf4k40eeesms15mrs5iis
slug: semantic-versioning-for-dummies-45c7fe04a1f8
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316772002/AYjJVb-6P.jpeg
tags: javascript, ecmascript, codenewbies

---

Photo by [Ross Findon](https://unsplash.com/@rossf?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Recently, I wrote a post about [things developers say and what they mean](https://medium.com/the-non-traditional-developer/things-developers-say-and-what-they-mean-48956b6111c1). I had a lot of fun writing it and enjoyed the comments that the post generated. There are a lot more things that I wanted to write about and I plan on doing a follow-up post in the future with more things, but there was one bit of lingo that I felt deserved more than a single paragraph explanation: Semver.

What is **Semver?** Semver is short for semantic versioning. Semantic Versioning is a standardized way to give meaning to your software releases. It’s a way for software authors to communicate succinctly to the consumers of their software important info they should know about this release.

Semver is represented by just three numbers separated by periods. For example, the current version of lodash, as of the publication date of this post, is 4.17.11, which you can find at their [github](https://github.com/lodash/lodash%5C) or [npm](https://www.npmjs.com/package/lodash) profile page. With this number I can easily tell all the compatibility information I need and can make decisions on if I should upgrade to the latest revision and how much work that may take to do that.

Each number is a different level of revision, depending on what was changed in the revision. Reading from left to write the number represents the current Major release, the current Minor release of the current Major release, and the current Patch release of the current Minor release(Major.Minor.Patch). Using the Lodash example above, 4.17.11 means the 11th patch since the 17th minor release, since the 4th major release. When a number is increased all numbers to the right start back at zero.

### Patch

A Patch release is used to signify that the code changes in this revision has not added any new features or API changes and is backward compatible with the previous version. It is most typically used to signify a bug fix. The most important thing to know is that the code has not changed the way it is used. Using the lodash example above, if a new patch revision is sent out then the version would be 4.17.12

### Minor

A Minor release is used to signify that functionality has been added, but the code is otherwise backward compatible. Keeping with the lodash example, if a new function is added or a new optional parameter is added to an existing function then the version would be 4.18.0 . The most important thing to remember is that this added functionality is optional and by upgrading to this version should not require code changes on the part of the user

### Major

When you make changes to the public API and the code is no longer backward compatible, then you have made “breaking” changes. This requires a major revision increase. This can mean a feature was removed or functionality has changed that requires the user to make changes to the code to accept the update. Using the same example, a major release would by 5.0.0

### Pre-release

Things are slightly different if the Major version is a zero. A zero Major version number indicates that the software is in rapid development and does not have a stable API. This also means each minor increase can have breaking changes. An example of this is React Native which is currently on version 0.57.8 This means that to upgrade to version 57 from 56 may require code changes and may not be compatible with other dependencies in the project. Patch updates should still be considered backward compatible

### Why is this important?

After reading all this you might be thinking, “I don’t plan on publishing a library or an npm package. Why is knowing this helpful?” First of all, I highly recommend you publish a package. You will learn a lot by going through the process. Secondly, as a developer who brings in these libraries and packages, it’s important to keep them up to date and this versioning system helps make decisions to update your dependencies not just easier to make, but automated.

When you install a package from npm you will notice that your package.json will be updated and will eventually look something like this:

```json
{
  "dependencies": {
    "react": "^16.6.3",

    "react-clean-form": "^2.1.2",

    "react-dom": "^16.6.3",

    "styled-components": "^4.1.1"
  }
}
```

You will notice that in front of each of their version numbers is a carat. What this tells npm (or yarn) that you want the latest release that is still backwards compatible with the version number. If you wanted to make sure that you only got patch updates and not minor version updates, then you would add a tilde instead of the carat like this: `"react": "~16.6.3"` and finally, if you just put the version number in, then you will only get that specific version. Basically, you are indicating at what level of backward compatibility are you comfortable with.

This convention allows your package manager to make smart decisions for you. If I was to clone the above project and run npm install. I would not get react version 16.6.3, I would get version 16.7.0 instead. This is because 16.7.0 is still backward compatible with 16.6.3, but is more up to date with the latest patches and features. When you run npm update, it does not just install the latest backward compatible version according to your rules, it also bumps the version number in your package.json as well. When you run npm audit -fix, this tells npm to run npm update on those packages that have known security issues so you can get the latest patches. If the fix is in a version that doesn’t fit your rules of comfort, it will let you know you need to fix it manually.

Semver is a powerful tool. With three numbers, authors can communicate very important information to the consumers of their software. This allows us as consumers to automate the tedious task of keeping our software up to date according to our comfort level and spend more time and the tasks we love doing: writing amazing code that solves problems. For a more complete explanation of semver, I would recommend checking out [semver.org](https://semver.org/)
