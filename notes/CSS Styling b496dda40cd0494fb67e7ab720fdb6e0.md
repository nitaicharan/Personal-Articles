# CSS Styling

Created: June 16, 2022 9:59 PM
Finished: Yes
Finished 📅: June 16, 2022
Order: 15
Source: https://nextjs.org/learn/basics/assets-metadata-css/css-styling

![learn-twitter.png](CSS%20Styling%20b496dda40cd0494fb67e7ab720fdb6e0/learn-twitter.png)

## CSS Styling

Let’s now talk about **CSS styling**.

As you can see, our index page ([http://localhost:3000](http://localhost:3000/)) already has some styles. If you take a look at `pages/index.js`, you should see code like this:

```
<style jsx>{`
  …
`}</style>

```

This page is using a library called [styled-jsx](https://github.com/vercel/styled-jsx). It’s a “CSS-in-JS” library — it lets you write CSS within a React component, and the CSS styles will be *scoped* (other components won’t be affected).

Next.js has built-in support for [styled-jsx](https://github.com/vercel/styled-jsx), but you can also use other popular CSS-in-JS libraries such as [styled-components](https://github.com/vercel/next.js/tree/canary/examples/with-styled-components) or [emotion](https://github.com/vercel/next.js/tree/canary/examples/with-emotion).

### Writing and Importing CSS

Next.js has [built-in support for CSS](https://nextjs.org/docs/basic-features/built-in-css-support) and Sass which allows you to import `.css` and `.scss` files.

Using popular CSS libraries like [Tailwind CSS](https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss) is also supported.

In this lesson, we’ll talk about how to write and import CSS files in Next.js. We’ll also talk about Next.js’s built-in support for [CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support#adding-component-level-css) and [Sass](https://nextjs.org/docs/basic-features/built-in-css-support#sass-support). Let’s dive in!