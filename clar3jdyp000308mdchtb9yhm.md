---
title: "Breaking Bedrock"
datePublished: Mon Nov 21 2022 18:03:19 GMT+0000 (Coordinated Universal Time)
cuid: clar3jdyp000308mdchtb9yhm
slug: breaking-bedrock
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/J8X3iKaLheA/upload/v1669012876654/uEVloM62x.jpeg
tags: css-frameworks, css, reactjs, layout

---

A few years ago, I started what ended up becoming the Bedrock Layout Primitive library. What began as a bit of a "revenge" driven project ended up becoming a passion. It resulted in an opportunity to [create a course on composing layouts in react](https://www.newline.co/courses/composing-layouts-in-react) and speak at several conferences.  

In the meantime, Bedrock has continued to grow and evolve as a library. More primitives have been added, the doc site was moved to Storybook, and visual regression testing was added through chromatic. But the most significant addition out of all of the additions has to be the `@bedrock-layout/css` framework. Pretty much the same developer experience as the React library, using only CSS and no JavaScript.

## Things have changed

At the same time that Bedrock Layout has been evolving, so has everything else. CSS and the three major browsers have hit some fantastic strides regarding CSS. They have been releasing new features on a much more consistent basis.

Another thing that has changed is that `styled-components` and CSS-in-JS tools, in general, have started to fall out as popular tools. There are many reasons why this is so. Nonetheless, building a library on top of `styled-components` means that Bedrock's adoption has been limited to only those projects that are willing to use `styled-components`.

In addition to this problem, maintaining both a CSS library and a React library built on `styled-components` means that I have to duplicate my effort every time I need to make a change. This increases the chance of divergences as well as just simple copy-paste errors.

Finally, to avoid breaking changes, we have maintained deprecated APIs even though they were no longer encouraged nor documented. All of these things have brought me to the conclusion that Bedrock needed to do a significant overhaul, or in other words, a breaking change.

## Announcing The New and Improved Bedrock Layout Primitives

There are two significant changes that Bedrock Layout Primitives has made. The first is that Bedrock Layout no longer uses `styled-components`. It instead depends on its internal Bedrock Layout CSS, which you can install at `@bedrock-layout/css`. This now means you need to [import the appropriate CSS files](https://www.bedrock-layout.dev/?path=/docs/bedrock-layout-css-a-css-only-version--page) for the primitives you are using or simply import them all:

```javascript
import "@bedrock-layout/css";
import "@bedrock-layout/css/lib/reset.css";
```

With this change, each package size has been cut in half and no longer has any runtime implications other than React itself. This makes Bedrock Layout great for any of your apps, no matter the React framework your team has chosen to adopt.

The other significant change has been that Bedrock Layout will no longer support its own spacing and sizes scheme. In the past, Bedrock's default spacing scheme was built on T-shirt sizes representing multiples of `1rem`. It worked fine, but it doesn't make sense for Bedrock to maintain another spacing scheme when there are so many better ones.  

With this new change, Bedrock has adopted a spacing and sizes theme based on [open-props](https://open-props.style/#sizes). Bedrock Layout now uses the following values:

```javascript
{
  space:{
    size000: "-.5rem",
    size00: "-.25rem",
    size1: ".25rem",
    size2: ".5rem",
    size3: "1rem",
    size4: "1.25rem",
    size5: "1.5rem",
    size6: "1.75rem",
    size7: "2rem",
    size8: "3rem",
    size9: "4rem",
    size10: "5rem",
    size11: "7.5rem",
    size12: "10rem",
    size13: "15rem",
    size14: "20rem",
    size15: "30rem",
  },
  sizes: {
    sizeContent1: "20ch",
    sizeContent2: "45ch",
    sizeContent3: "60ch",
    sizeHeader1: "20ch",
    sizeHeader2: "25ch",
    sizeHeader3: "35ch",
    sizeXxs: "240px",
    sizeXs: "360px",
    sizeSm: "480px",
    sizeMd: "768px",
    sizeLg: "1024px",
    sizeXl: "1440px",
    sizeXxl: "1920px",
  }
}
```

In addition, some other breaking but insignificant changes have been that Bedrock has dropped the use of `prop-types` and uses only named exports for all the packages. Once again, not substantial, but breaking nonetheless.

## What has really changed?

Not as much as it seems. Bedrock still works the same. It still helps you compose complex layouts from simple layout primitives.  

```javascript
export function Hero() {
  return (
    <Stack>
      <Inline>{/* */}</Inline>
      <Split>
        <Cover>
          <Stack>
            <h1>{/* */}</h1>
            <p>{/* */}</p>
            <Inline>
              <button>{/* */}</button>
              <button>{/* */}</button>
            </Inline>
          </Stack>
        </Cover>
        <Frame>
          <img />
        </Frame>
      </Split>
    </Stack>
  );
}
```

You can still bring your own spacing and sizing scheme via ThemeProvider and declaration merging:

```typescript
import { ThemeProvider } from '@bedrock-layout/spacing-constants';

declare module "@bedrock-layout/spacing-constants" {
  export interface DefaultTheme {
    space: {
      "0x": number;
      "1x": CSSLength;
      "3x": CSSLength;
    };
  }
}

const space = {
    "0x": 0,
    "1x":'45px',
    "3x":'100ch'
}

<ThemeProvider theme={{ space }}>
    <Stack gutter="1x">
     {...}
    </Stack>
</ThemeProvider>;
```

You can also still provide values directly into the size and spacing values as usual:

```typescript
<Stack gutter="var(--size-3)">
    <Split gutter='2rem' minItemWidth="30ch" switchAt="var(--size-content-1)">
        {/* ... */}
   </Split>
</Stack>
```
It just does all this better under the hood than it used to.

Check out this code pen and play around with it yourself: https://codesandbox.io/s/bedrock-layout-ts-template-64xwz?file=/src/index.tsx

## What is on the horizon?

A significant initiative we have going forward is translating all the docs into as many languages as possible. With the help of a contributor, Carlos Cabrera, the landing page has been translated into Spanish. I want to put in the work and make it easier for future contributors to solve or fix the translation of any of the pages on the doc site.

I also would like to do more to bring the CSS Framework front and center and less as a second-class citizen.

I also feel that there needs to be more examples and recipes for using Bedrock Layout. If you would like to help out with any of these initiatives, [please look at contributing.](https://www.bedrock-layout.dev/?path=/docs/overview-contributing--page)
