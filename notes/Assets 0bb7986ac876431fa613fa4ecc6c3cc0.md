# Assets

Created: June 6, 2022 4:52 PM
Finished: Yes
Finished 📅: June 6, 2022
Order: 12
Source: https://nextjs.org/learn/basics/assets-metadata-css/assets

![learn-twitter.png](Assets%200bb7986ac876431fa613fa4ecc6c3cc0/learn-twitter.png)

## Assets

Next.js can serve **static assets**, like images, under **the top-level `[public` directory](https://nextjs.org/docs/basic-features/static-file-serving)**. Files inside `public` can be referenced from the root of the application similar to `[pages](https://nextjs.org/docs/basic-features/pages)`.

The `public` directory is also useful for `robots.txt`, Google Site Verification, and any other static assets. Check out the documentation for [Static File Serving](https://nextjs.org/docs/basic-features/static-file-serving) to learn more.

### Download Your Profile Picture

First, let's retrieve your profile picture.

- **Download** your profile picture in `.jpg` format (or [use this file](https://github.com/vercel/next-learn/blob/master/basics/basics-final/public/images/profile.jpg)).
- Save the picture as `profile.jpg` in the `public/images` directory.
- The image size can be around 400px by 400px.
- You may remove the unused SVG logo file directly under the `[public` directory](https://nextjs.org/docs/basic-features/static-file-serving).

### Unoptimized Image

With regular HTML, you would add your profile picture as follows:

```html
<img src="/images/profile.jpg" alt="Your Name" />
```

However, this means you have to manually handle:

- Ensuring your image is responsive on different screen sizes
- Optimizing your images with a third-party tool or library
- Only loading images when they enter the viewport

And more. Instead, Next.js provides an `Image` component out of the box to handle this for you.

### Image Component and Image Optimization

`[next/image](https://nextjs.org/docs/api-reference/next/image)` is an extension of the HTML `<img>` element, evolved for the modern web.

Next.js also has support for Image Optimization by default. This allows for resizing, optimizing, and serving images in modern formats like [WebP](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#webp) when the browser supports it. This avoids shipping large images to devices with a smaller viewport. It also allows Next.js to automatically adopt future image formats and serve them to browsers that support those formats.

Automatic Image Optimization works with any image source. Even if the image is hosted by an external data source, like a CMS, it can still be optimized.

### Using the Image Component

Instead of optimizing images at build time, Next.js optimizes images on-demand, as users request them. Unlike static site generators and static-only solutions, your build times aren't increased, whether shipping 10 images or 10 million images.

Images are lazy loaded by default. That means your page speed isn't penalized for images outside the viewport. Images load as they are scrolled into viewport.

Images are always rendered in such a way as to avoid [Cumulative Layout Shift](https://web.dev/cls/), a [Core Web Vital](https://web.dev/vitals/#core-web-vitals) that Google is going to [use in search ranking](https://webmasters.googleblog.com/2020/05/evaluating-page-experience.html).

Here's an example using `[next/image](https://nextjs.org/docs/api-reference/next/image)` to display our profile picture. The `height` and `width` props should be the desired rendering size, with an aspect ratio identical to the source image.

**Note:** We'll use this component later in "Polishing Layout", no need to copy it yet.

```tsx
import Image from 'next/image';

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
);

```

> To learn more about Automatic Image Optimization, check out the documentation.
> 
> 
> To learn more about the `Image` component, check out the [API reference for `next/image`](https://nextjs.org/docs/api-reference/next/image).
> 

**Quick Review**: What does `next/image` simplify for you?