# Pages in Next.js

Created: June 4, 2022 12:06 PM
Finished: Yes
Finished 📅: June 4, 2022
Order: 7
Source: https://nextjs.org/learn/basics/navigate-between-pages/pages-in-nextjs

![learn-twitter.png](Pages%20in%20Next%20js%20af90a54b00e24f53b753c86a40c5627a/learn-twitter.png)

## Navigate Between Pages

In Next.js, a page is a React Component exported from a file in the `[pages` directory](https://nextjs.org/docs/basic-features/pages).

Pages are associated with a route based on their **file name**. For example, in development:

- `pages/index.js` is associated with the `/` route.
- `pages/posts/first-post.js` is associated with the `/posts/first-post` route.

We already have the `pages/index.js` file, so let’s create `pages/posts/first-post.js` to see how it works.

### Create a New Page

Create the `posts` directory under `pages`.

Create a file called `first-post.js` inside the `posts` directory with the following content:

```
export default function FirstPost() {
  return <h1>First Post</h1>;
}

```

The component can have any name, but you must export it as a `default` export.

Now, make sure that the development server is running and visit [http://localhost:3000/posts/first-post](http://localhost:3000/posts/first-post). You should see the page:

This is how you can create different pages in Next.js.

Simply create a JS file under the `[pages` directory](https://nextjs.org/docs/basic-features/pages), and the path to the file becomes the URL path.

In a way, this is similar to building websites using HTML or PHP files. Instead of writing HTML you write JSX and use React Components.

Let's add a link to the newly added page so that we can navigate to it from the homepage.

**Quick Review**: If you wanted to add a new route `/authors/me`, what would the file name be (including the directory)?