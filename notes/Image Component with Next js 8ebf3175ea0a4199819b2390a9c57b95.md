# Image Component with Next.js

Created: June 6, 2022 5:43 PM
Finished: No
Source: https://image-component.nextjs.gallery/

This page demonstrates the usage of the [next/image](https://nextjs.org/docs/api-reference/next/image) component with live examples.

This component is designed to [automatically optimize](https://nextjs.org/docs/basic-features/image-optimization) images on-demand as the browser requests them.

## Layout

The `layout` property tells the image to respond differently depending on the device size or the container size.

Select a layout below and try resizing the window or rotating your device to see how the image reacts.

## Placeholder

The `placeholder` property tells the image what to do while loading.

You can optionally enable a blur-up placeholder while the high resolution image loads.

Try it out below (you may need to disable cache in dev tools to see the effect if you already visited):

- [placeholder="blur"](https://image-component.nextjs.gallery/placeholder)

## Internal Image

The following is an example of a reference to an internal image from the `public` directory.

This image is intentionally large so you have to scroll down to the next image.

## External Image

The following is an example of a reference to an external image at `assets.vercel.com`.

External domains must be configured in `next.config.js` using the `domains` property.

## Learn More

You can optionally configure a cloud provider, device sizes, and more!

Checkout the [Image Optimization documentation](https://nextjs.org/docs/basic-features/image-optimization) to learn more.