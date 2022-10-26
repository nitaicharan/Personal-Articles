# Getting Started

Created: May 26, 2022 7:54 AM
Finished: No
Source: https://nextjs.org/docs/getting-started

![documentation.png](Getting%20Started%209f1c3de0be124cc895744e2204f91877/documentation.png)

Welcome to the Next.js documentation!

If you're new to Next.js, we recommend starting with the [learn course](https://nextjs.org/learn/basics/create-nextjs-app).

The interactive course with quizzes will guide you through everything you need to know to use Next.js.

If you have questions about anything related to Next.js, you're always welcome to ask our community on [GitHub Discussions](https://github.com/vercel/next.js/discussions).

- [Node.js 12.22.0](https://nodejs.org/) or later
- MacOS, Windows (including WSL), and Linux are supported

We recommend creating a new Next.js app using `create-next-app`, which sets up everything automatically for you. To create a project, run:

```
npx create-next-app@latest
# or
yarn create next-app
# or
pnpm create next-app

```

If you want to start with a TypeScript project you can use the `--typescript` flag:

```
npx create-next-app@latest --typescript
# or
yarn create next-app --typescript
# or
pnpm create next-app -- --typescript

```

After the installation is complete:

- Run `npm run dev` or `yarn dev` or `pnpm dev` to start the development server on `http://localhost:3000`
- Visit `http://localhost:3000` to view your application
- Edit `pages/index.js` and see the updated result in your browser

For more information on how to use `create-next-app`, you can review the `[create-next-app` documentation](https://nextjs.org/docs/api-reference/create-next-app).

Install `next`, `react` and `react-dom` in your project:

```
npm install next react react-dom
# or
yarn add next react react-dom
# or
pnpm add next react react-dom

```

Open `package.json` and add the following `scripts`:

```
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "next lint"
}

```

These scripts refer to the different stages of developing an application:

- `start` - Runs `[next start](https://nextjs.org/docs/api-reference/cli#production)` to start a Next.js production server

Create two directories `pages` and `public` at the root of your application:

- `pages` - Associated with a route based on their file name. For example `pages/about.js` is mapped to `/about`
- `public` - Stores static assets such as images, fonts, etc. Files inside `public` directory can then be referenced by your code starting from the base URL (`/`).

Next.js is built around the concept of [pages](https://nextjs.org/docs/basic-features/pages). A page is a [React Component](https://reactjs.org/docs/components-and-props.html) exported from a `.js`, `.jsx`, `.ts`, or `.tsx` file in the `pages` directory. You can even add [dynamic route](https://nextjs.org/docs/routing/dynamic-routes) parameters with the filename.

Inside the `pages` directory add the `index.js` file to get started. This is the page that is rendered when the user visits the root of your application

Populate `pages/index.js` with the following contents:

```
function HomePage() {
  return <div>Welcome to Next.js!</div>
}

export default HomePage

```

After the set up is complete:

- Run `npm run dev` or `yarn dev` or `pnpm dev` to start the development server on `http://localhost:3000`
- Visit `http://localhost:3000` to view your application
- Edit `pages/index.js` and see the updated result in your browser

So far, we get:

In addition, any Next.js application is ready for production from the start. Read more in our [Deployment documentation](https://nextjs.org/docs/deployment).

For more information on what to do next, we recommend the following sections: