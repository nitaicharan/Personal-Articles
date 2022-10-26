# Client-Side Navigation

Created: June 4, 2022 12:32 PM
Finished: Yes
Finished 📅: June 4, 2022
Order: 9
Source: https://nextjs.org/learn/basics/navigate-between-pages/client-side

## Client-Side Navigation

The `[Link](https://nextjs.org/docs/api-reference/next/link)` component enables **client-side navigation** between two pages in the same Next.js app.

Client-side navigation means that the page transition happens *using JavaScript*, which is faster than the default navigation done by the browser.

Here’s a simple way you can verify it:

- Use the browser’s developer tools to change the `background` CSS property of `<html>` to `yellow`.
- Click on the links to go back and forth between the two pages.
- You’ll see that the yellow background persists between page transitions.

This shows that the browser does *not* load the full page and client-side navigation is working.

![Client-Side%20Navigation%20ea000ed6e3654d6089d72a76a05b3333/client-side.gif](Client-Side%20Navigation%20ea000ed6e3654d6089d72a76a05b3333/client-side.gif)

If you’ve used `<a href="…">` instead of `<Link href="…">` and did this, the background color will be cleared on link clicks because the browser does a full refresh.

### Code splitting and prefetching

Next.js does code splitting automatically, so each page only loads what’s necessary for that page. That means when the homepage is rendered, the code for other pages is not served initially.

This ensures that the homepage loads quickly even if you have hundreds of pages.

Only loading the code for the page you request also means that pages become isolated. If a certain page throws an error, the rest of the application would still work.

Furthermore, in a production build of Next.js, whenever `[Link](https://nextjs.org/docs/api-reference/next/link)` components appear in the browser’s viewport, Next.js automatically **prefetches** the code for the linked page in the background. By the time you click the link, the code for the destination page will already be loaded in the background, and the page transition will be near-instant!

### Summary

Next.js automatically optimizes your application for the best performance by code splitting, client-side navigation, and prefetching (in production).

You create routes as files under `[pages](https://nextjs.org/docs/basic-features/pages)` and use the built-in `[Link](https://nextjs.org/docs/api-reference/next/link)` component. No routing libraries are required.

You can learn more about the `Link` component [in the API reference for `next/link`](https://nextjs.org/docs/api-reference/next/link) and routing in general [in the routing documentation](https://nextjs.org/docs/routing/introduction).

> Note: If you need to link to an external page outside the Next.js app, just use an <a> tag without Link.
> 
> 
> If you need to add attributes like, for example, `className`, add it to the `a` tag, *not* to the `Link` tag. [Here’s an example](https://github.com/vercel/next-learn/blob/master/basics/snippets/link-classname-example.js).
> 

**Quick Review**: A user opens their browser and navigates to `your-blog.com/posts/first-post`. What JavaScript is initially loaded for this page?