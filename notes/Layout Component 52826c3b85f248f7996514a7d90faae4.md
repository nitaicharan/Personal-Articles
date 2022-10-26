# Layout Component

Created: June 16, 2022 10:00 PM
Finished: Yes
Finished 📅: June 16, 2022
Order: 16
Source: https://nextjs.org/learn/basics/assets-metadata-css/layout-component

![learn-twitter.png](Layout%20Component%2052826c3b85f248f7996514a7d90faae4/learn-twitter.png)

## Layout Component

First, let’s create a **Layout** component which will be shared across all pages.

- Create a top-level directory called `components`.
- Inside `components`, create a file called `layout.js` with the following content:

```
export default function Layout({ children }) {
  return <div>{children}</div>;
}

```

Then, open `pages/posts/first-post.js`, add an import for the `Layout` component, and make it the outermost component:

```
import Head from 'next/head';
import Link from 'next/link';
import Layout from '../../components/layout';

export default function FirstPost() {
  return (
    <Layout>
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </Layout>
  );
}

```

### Adding CSS

Now, let’s add some styles to the `Layout` component. To do so, we’ll use [CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support#adding-component-level-css), which lets you import CSS files in a React component.

Create a file called `components/layout.module.css` with the following content:

```
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}

```

> Important: To use CSS Modules, the CSS file name must end with .module.css.
> 

To use this `container` class inside `components/layout.js`, you need to:

- Import the CSS file and assign a name to it, like `styles`
- Use `styles.container` as the `className`

Open `components/layout.js` and replace its content with the following:

```
import styles from './layout.module.css';

export default function Layout({ children }) {
  return <div className={styles.container}>{children}</div>;
}

```

If you go to [http://localhost:3000/posts/first-post](http://localhost:3000/posts/first-post) now, you should see that the text is now inside a centered container:

### Automatically Generates Unique Class Names

Now, if you take a look at the HTML in your browser’s devtools, you’ll notice that the `div` rendered by the `Layout` component has a class name that looks like `layout_container__...`:

This is what [CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support#adding-component-level-css) does: *It automatically generates unique class names*. As long as you use CSS Modules, you don’t have to worry about class name collisions.

Furthermore, Next.js’s code splitting feature works on [CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support#adding-component-level-css) as well. It ensures the minimal amount of CSS is loaded for each page. This results in smaller bundle sizes.

[CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support#adding-component-level-css) are extracted from the JavaScript bundles at build time and generate `.css` files that are loaded automatically by Next.js.

**Quick Review**: Which of the following styling methods does Next.js **not** have built-in support for?