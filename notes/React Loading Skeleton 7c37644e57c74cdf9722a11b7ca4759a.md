# React Loading Skeleton

Created: May 7, 2022 11:02 AM
Finished: No
Source: https://github.com/dvtng/react-loading-skeleton
Tags: #react

![React%20Loading%20Skeleton%207c37644e57c74cdf9722a11b7ca4759a/logo.svg](React%20Loading%20Skeleton%207c37644e57c74cdf9722a11b7ca4759a/logo.svg)

# React Loading Skeleton

Make beautiful, animated loading skeletons that automatically adapt to your app.

### [Open on CodeSandbox](https://codesandbox.io/s/react-loading-skeleton-3xwil?file=/src/App.tsx)

[React%20Loading%20Skeleton%207c37644e57c74cdf9722a11b7ca4759a/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6c3049796b346241416a6163334155326b2f67697068792e676966](React%20Loading%20Skeleton%207c37644e57c74cdf9722a11b7ca4759a/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6c3049796b346241416a6163334155326b2f67697068792e676966)

Learn about the [changes in version 3](https://github.com/dvtng/react-loading-skeleton/releases/tag/v3.0.0), or view the [v2 documentation](https://github.com/dvtng/react-loading-skeleton/tree/v2#readme).

## Basic Usage

Install via one of:

```
yarn add react-loading-skeleton
npm install react-loading-skeleton
```

```
import Skeleton from 'react-loading-skeleton'
import 'react-loading-skeleton/dist/skeleton.css'

<Skeleton /> // Simple, single-line loading skeleton
<Skeleton count={5} /> // Five-line loading skeleton
```

## Principles

### Adapts to the styles you have defined

The `Skeleton` component should be used directly in your components in place of content that is loading. While other libraries require you to meticulously craft a skeleton screen that matches the font size, line height, and margins of your content, the `Skeleton` component is automatically sized to the correct dimensions.

For example:

```
function BlogPost(props) {
 return (
 <div>
 <h1>{props.title || <Skeleton />}</h1>
 {props.body || <Skeleton count={10} />}
 </div>
 )
}
```

...will produce correctly-sized skeletons for the heading and body without any further configuration.

This ensures the loading state remains up-to-date with any changes to your layout or typography.

### Don't make dedicated skeleton screens

Instead, make components with *built-in* skeleton states.

This approach is beneficial because:

1. It keeps styles in sync.
2. Components should represent all possible states — loading included.
3. It allows for more flexible loading patterns. In the blog post example above, it's possible to have the title load before the body, while having both pieces of content show loading skeletons at the right time.

## Theming

Customize individual skeletons with props, or render a `SkeletonTheme` to style all skeletons below it in the React hierarchy:

```
import Skeleton, { SkeletonTheme } from 'react-loading-skeleton'

return (
 <SkeletonTheme baseColor="#202020" highlightColor="#444">
 <p>
 <Skeleton count={3} />
 </p>
 </SkeletonTheme>
)
```

## Props Reference

### `Skeleton` only

[Untitled](React%20Loading%20Skeleton%207c37644e57c74cdf9722a11b7ca4759a/Untitled%20Database%20303c04bda4e5456580c941abeacb8d22.csv)

### `Skeleton` and `SkeletonTheme`

[Untitled](React%20Loading%20Skeleton%207c37644e57c74cdf9722a11b7ca4759a/Untitled%20Database%207fb6a2abacc14494a1c5122bf55f051d.csv)

## Examples

### Custom Wrapper

There are two ways to wrap a skeleton in a container:

```
function Box({ children }: PropsWithChildren<unknown>) {
 return (
 <div
 style={{
 border: '1px solid #ccc',
 display: 'block',
 lineHeight: 2,
 padding: '1rem',
 marginBottom: '0.5rem',
 width: 100,
 }}
 >
 {children}
 </div>
 )
}

// Method 1: Use the wrapper prop
const wrapped1 = <Skeleton wrapper={Box} count={5} />

// Method 2: Do it "the normal way"
const wrapped2 = (
 <Box>
 <Skeleton />
 </Box>
)
```

### The height of my container is off by a few pixels!

In the example below, the height of the `<div>` will be slightly larger than 30 even though the `react-loading-skeleton` element is exactly 30px.

```
<div>
 <Skeleton height={30} />
</div>
```

This is a consequence of how `line-height` works in CSS. If you need the `<div>` to be exactly 30px tall, set its `line-height` to 1. [See here](https://github.com/dvtng/react-loading-skeleton/issues/23#issuecomment-939231878) for more details.

## Contributing

Contributions are welcome! See `CONTRIBUTING.md` to get started.

## Acknowledgements

Our logo is based off an image from [Font Awesome](https://fontawesome.com/license/free). Thanks!