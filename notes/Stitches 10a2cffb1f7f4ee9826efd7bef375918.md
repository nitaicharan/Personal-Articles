# Stitches

Created: May 7, 2022 11:07 AM
Finished: No
Source: https://stitches.dev/
Tags: #tool

![social.png](Stitches%2010a2cffb1f7f4ee9826efd7bef375918/social.png)

CSS-in-JS with near-zero runtime, SSR, multi-variant support, and a best-in-class developer experience.

### Stats at a glance

`Variants`

Variants are a first-class citizen of Stitches. With multiple variants, compound variants, and default variants, you can design composable component APIs which are typed automatically.

```
const Button = styled('button', {

```

`Theming`

Stitches has built-in solutions for tokens and theming, which use CSS variables under-the-hood. You can define multiple themes, then expose them to any part of your app.

```
    fonts: {},
    space: {},
    sizes: {},
    radii: {},

```

[Been using @stitchesjs for a week on an actual product and I've never had such a smooth collaboration, shared vocabulary, and mutual understanding with our designer. Setting up tokens and being very systematic and constrained about the UI we build has never been easier.](https://twitter.com/raunofreiberg/status/1309087632308277251)

[The cool thing for me as a designer was seeing the systematic approach to using design tokens at a level I could understand 😂 I believe the gap between design and development is finally narrowing and solutions like Stitches and Modulz speed up this process.](https://twitter.com/1stfloor/status/1364254660119453698)

[I haven't been much of a fan of js object syntax for css in the past, but I've been trying out @stitchesjs on a side project and it's quite fun so far.
The performance promises and type-hinting make it really appealing right away. Good docs as well.](https://twitter.com/JimmyDCleveland/status/1332719743510343682)

[Eu tenho faro para identificar tecnologias que se popularizaram: gravei sobre Laravel em 2011, sobre Vue em 2015 e Tailwind em 2020.
Algo me diz que @stitchesjs: estará no mesmo nível de popularidade em breve!](https://twitter.com/vedovelli74/status/1366752905064251393)

`Smart tokens`

Tokens automatically map to the most appropriate scale—with a simple syntax—for a smooth developer experience. You can customise the default mapping with our `themeMap` object, or override the default on a case-by-case basis.

```
const Button = styled("button", {

```

`Utils`

Invent your own custom CSS properties with our utils feature. Speed up your workflow by abbreviating CSS properties, grouping multiple CSS properties together, or simplifying a tricky syntax.

```
export const { styled, css } = createStitches({
    pt: (value) => ({
    pr: (value) => ({
    pb: (value) => ({
    pl: (value) => ({
    px: (value) => ({
    py: (value) => ({
    size: (value) => ({
    linearGradient: (value) => ({

```

# Features

A fully-featured styling library.

### Community

Get involved in our community. Everyone is welcome!