# Routing

Created: June 4, 2022 12:33 PM
Finished: Yes
Finished 📅: June 4, 2022
Source: https://nextjs.org/docs/routing/introduction

![documentation.png](Routing%20f7281976b6bf49c683f462a55edbddc4/documentation.png)

Next.js has a file-system based router built on the [concept of pages](https://nextjs.org/docs/basic-features/pages).

When a file is added to the `pages` directory, it's automatically available as a route.

The files inside the `pages` directory can be used to define most common patterns.

The router will automatically route files named `index` to the root of the directory.

- `pages/index.js` → `/`
- `pages/blog/index.js` → `/blog`

The router supports nested files. If you create a nested folder structure, files will automatically be routed in the same way still.

- `pages/blog/first-post.js` → `/blog/first-post`
- `pages/dashboard/settings/username.js` → `/dashboard/settings/username`

To match a dynamic segment, you can use the bracket syntax. This allows you to match named parameters.

- `pages/blog/[slug].js` → `/blog/:slug` (`/blog/hello-world`)
- `pages/[username]/settings.js` → `/:username/settings` (`/foo/settings`)
- `pages/post/[...all].js` → `/post/*` (`/post/2020/id/title`)

> 
> 
> 
> Check out the [Dynamic Routes documentation](https://nextjs.org/docs/routing/dynamic-routes) to learn more about how they work.
> 

The Next.js router allows you to do client-side route transitions between pages, similar to a single-page application.

A React component called `Link` is provided to do this client-side route transition.

```
import Link from 'next/link'

function Home() {
  return (
    <ul>
      <li>
        <Link href="/">
          <a>Home</a>
        </Link>
      </li>
      <li>
        <Link href="/about">
          <a>About Us</a>
        </Link>
      </li>
      <li>
        <Link href="/blog/hello-world">
          <a>Blog Post</a>
        </Link>
      </li>
    </ul>
  )
}

export default Home

```

The example above uses multiple links. Each one maps a path (`href`) to a known page:

- `/` → `pages/index.js`
- `/about` → `pages/about.js`
- `/blog/hello-world` → `pages/blog/[slug].js`

Any `<Link />` in the viewport (initially or through scroll) will be prefetched by default (including the corresponding data) for pages using [Static Generation](https://nextjs.org/docs/basic-features/data-fetching/get-static-props). The corresponding data for [server-rendered](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props) routes is *not* prefetched.

You can also use interpolation to create the path, which comes in handy for [dynamic route segments](https://nextjs.org/docs/routing/introduction#dynamic-route-segments). For example, to show a list of posts which have been passed to the component as a prop:

```
import Link from 'next/link'

function Posts({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${encodeURIComponent(post.slug)}`}>
            <a>{post.title}</a>
          </Link>
        </li>
      ))}
    </ul>
  )
}

export default Posts

```

> 
> 
> 
> `[encodeURIComponent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)` is used in the example to keep the path utf-8 compatible.
> 

Alternatively, using a URL Object:

```
import Link from 'next/link'

function Posts({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link
            href={{
              pathname: '/blog/[slug]',
              query: { slug: post.slug },
            }}
          >
            <a>{post.title}</a>
          </Link>
        </li>
      ))}
    </ul>
  )
}

export default Posts

```

Now, instead of using interpolation to create the path, we use a URL object in `href` where:

- `pathname` is the name of the page in the `pages` directory. `/blog/[slug]` in this case.
- `query` is an object with the dynamic segment. `slug` in this case.

To access the `[router` object](https://nextjs.org/docs/api-reference/next/router#router-object) in a React component you can use `[useRouter](https://nextjs.org/docs/api-reference/next/router#userouter)` or `[withRouter](https://nextjs.org/docs/api-reference/next/router#withrouter)`.

In general we recommend using `[useRouter](https://nextjs.org/docs/api-reference/next/router#userouter)`.

The router is divided in multiple parts: