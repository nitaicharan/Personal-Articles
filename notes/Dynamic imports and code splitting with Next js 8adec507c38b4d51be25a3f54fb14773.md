# Dynamic imports and code splitting with Next.js

Created: August 31, 2022 3:59 PM
Finished: No
Source: https://blog.logrocket.com/dynamic-imports-code-splitting-next-js/

![Dynamic%20imports%20and%20code%20splitting%20with%20Next%20js%208adec507c38b4d51be25a3f54fb14773/dynamic-imports-code-splitting-next-js.png](Dynamic%20imports%20and%20code%20splitting%20with%20Next%20js%208adec507c38b4d51be25a3f54fb14773/dynamic-imports-code-splitting-next-js.png)

## Introduction

Optimizing performance in production environments can sometimes be an uphill task. Fine-tuning site performance cannot be overlooked, as the demerits cause slow web pages with bad UX. These sites tend to load slowly, have slow-rendering images, and in the long run, lead to increased bounce rates from site visitors as most users won’t be willing to wait for content to pop up.

In this tutorial, we will cover different patterns to speed-up site performance in a Next.js application.

### Goals and prerequisites

At the end of this article, readers will have a clear understanding of how they can maximize performance in a Next.js web application. We’ll cover the following:

- [What are dynamic imports and code splitting?](https://blog.logrocket.com/dynamic-imports-code-splitting-next-js/#dynamic-imports-code-splitting)

To follow along with this article, prior knowledge of the Next.js framework is required.

## What are dynamic imports and code splitting?

Dynamic imports, also known as code splitting, refers to the practice of dividing bundles of JavaScript code into smaller chunks, which are then pieced together and loaded into the runtime of an application as a means to drastically boost site performance.

It was developed as an upgrade to static imports in JavaScript, which is the standard way of adding imports for modules or components at the top level of a JavaScript module using the imports syntax.

While this is a commonly used method, there are some drawbacks where performance optimization is concerned, especially in cases such as:

- Large codebases, which create larger bundles size and result in longer load times because the build process compiles all required files into a single bundle
- Web pages that require certain user actions, such as clicking on a navigation menu item to trigger a page load. Here, the required page is only rendered when the navigation criteria is met and could trigger a slow initial page load when components are imported statically

## How do dynamic imports differ from static imports?

Unlike static imports, dynamic imports work by applying a method known as code splitting. Code splitting is the division of code into various bundles, which are arranged in parallel using a tree format, where modules are loaded dynamically — the modules are only imported and included in the JavaScript bundle when they are required. The more the code is split, the smaller the bundle size, and the faster the page loads.

This method creates multiple bundles that are dynamically loaded at the runtime of the webpage. Dynamic imports make use of import statements written as inline function calls.

Let’s look at a comparison. Suppose we wish to import a navigation component in our application; an example of static and dynamic imports for a navigation component is illustrated below:

Static import:

```
import Nav from './components/Nav'export default functionHome(){
  return (
    <div><Nav/></div>
  )}
```

Dynamic import:

```
import dynamic from "next/dynamic";import { Suspense } from "react";export default functionHome(){
  const Navigation = dynamic(()=> import("./components/Nav.js"), {
    suspense: true,
  });
  return (
    <div><Suspense fallback={<div>Loading...</div>}><Navigation/></Suspense></div>
  );}
```

Here, the navigation component has its relative part specified in the `import()` block. Note that `next/dynamic` does not allow template literals or variables to be used in the `import()` argument.

Also, `react/suspense` has a specified fallback element, which is displayed until the imported component is available.

## Benefits of dynamic imports in Next.js

Optimizing site performance as a result of implementing dynamic imports will, in turn, result in the following site benefits:

- Faster page loads: The speed at which a website loads and displays content is crucial, as your audience wants to get things done quickly and won’t stick around for slow web pages
    - Dynamic imports also have a positive impact on image load times
- Low bounce rates: The bounce rate, which refers to the rate at which users exit your webpage without interacting with the site, usually indicates (and is caused by) slow load times. A lower bounce rate usually means faster site performance
- Improved site interaction time: This deals with TTI, or Time to Interactive, the time intervals between when a user requests an action, and when the user gets a result. These interactions can include clicking links, scrolling through pages, entering inputs in a search field, adding items to a shopping cart, etc.
- Better site conversion rates: As more users gain satisfaction from using a well-optimized website, they’ll be more likely to convert

With all of these benefits, you are probably thinking about how to use dynamic imports in your application. Then, the big question is, how can we implement dynamic imports and code splitting in a Next.js application? The next section shows detailed steps on how you can achieve this.

## Implementing dynamic imports and code splitting in Next.js

Next.js makes it easy to create dynamic imports in a Next application through the `next/dynamic` module, as demonstrated above. The `next/dynamic` module implements lazy loading imports of React components, and is built on [React Lazy](https://reactjs.org/docs/code-splitting.html#reactlazy).

### More great articles from LogRocket:

- Don't miss a moment with [The Replay](https://lp.logrocket.com/subscribe-thereplay), a curated newsletter from LogRocket
- [Learn how to animate](https://blog.logrocket.com/animate-react-app-animxyz/) your React app with AnimXYZ
- [Explore Tauri](https://blog.logrocket.com/rust-solid-js-tauri-desktop-app/), a new framework for building binaries
- [Discover](https://blog.logrocket.com/best-typescript-orms/) popular ORMs used in the TypeScript landscape

It also makes use of the [React Suspense library](https://blog.logrocket.com/async-rendering-react-suspense/) to allow the application to put off loading components until they are needed, thereby improving initial loading performance due to lighter JavaScript builds.

### Dynamically importing named exports

Earlier in this article, we demonstrated importing a component using `next/dynamic`. But we can also make dynamic imports for functions or methods exported from another file. This is demonstrated as follows:

```
import React from 'react'export functionSayWelcome(){
  return (
    <div>Welcome to my application</div>
  )}const SayHello = ()=> {
  return (
    <div>SayHello</div>
  )}export default SayHello
```

In the code above, we have a component, `SayHello`, and a named import, `SayWelcome`. We can make a dynamic explicit import for just the `SayWelcome` method, as shown below:

```
import dynamic from "next/dynamic";import { Suspense } from "react";export default functionHome(){
  const SayWelcome = dynamic(
    ()=> import("./components/SayHello").then((res)=> res.SayWelcome)
  );
  return (
    <div><Suspense fallback={<div>Loading...</div>}><SayWelcome/></Suspense></div>
  );}
```

The above code imports the `SayHello` component and then returns the `SayWelcome` export from the response.

### Dynamically importing multiple components

Suppose we have `UserDetails` and `UserImage` components. We can import and display both components as follows:

```
import dynamic from 'next/dynamic'const details = dynamic(()=> import('./components/UserDetails'))const image = dynamic(()=> import('./components/UserImage'))functionUserAccount(){
  return (
    <div><h1>ProfilePage</h1><details /><image /></div>
  )}const App = ()=> {
  return (
    <><UserAccount/>)</>}exportdefaultApp
```

In the code above, we added dynamic imports for the `UserDetails` and `UserImage` components, then we put these components together into a single component, `UserAccount`. Finally, we returned the `UserAccount` component in the application.

## Dynamic imports for client-side rendering

With the `next/dynamic` module, we can also disable server-side rendering for imported components and render these components on the client-side instead. This is particularly suitable for components that do not require much user interaction or have external dependencies, such as APIs. This can be done by setting the `ssr` property to `false` when importing the component:

```
import dynamic from 'next/dynamic'const HeroItem = dynamic(()=> import('../components/HeroItem'), {
  ssr: false,})const App = ()=> {
  return (
    <><HeroItem/>)</>}
```

Here, the `HeroItem` component has server-side rendering set to `false`, hence it is rendered on the client-side instead.

### Dynamic imports for libraries

Apart from importing local components, we can also add a dynamic import for external dependencies.

For example, suppose we wish to [use Axios `fetch`](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/) to get data from an API whenever a user requests it. We can define a dynamic import for `Axios` and implement it, as shown below:

```
import styles from "../styles/Home.module.css";import { React, useState } from "react";export default functionHome(){
  const [search, setSearch] = useState("");
  let [response, setResponse] = useState([]);
  const api_url = `https://api.github.com/search/users?q=${search}&per_page=5`;

  return (
    <div className={styles.main}><input
        type="text"
        placeholder="Search Github Users"
        value={search}
        onChange={(e)=> setSearch(e.target.value)}/><button
        onClick={async()=>{// dynamically load the axios dependencyconst axios =(awaitimport("axios")).default;const res =await axios.get(api_url).then((res)=>{
            setResponse(res);});}}>SearchforGitHub users
      </button><div><h1>{search}Results</h1><ul>{response?.data ?(
            response && response?.data.items.map((item, index)=>(<span key={index}><p>{item.login}</p></span>))):(<p>NoResults</p>)}</ul></div></div>);}
```

In the code above, we have an input field to search for user names on GitHub. We use the `useState()` Hook to manage and update the state of the input field, and we have set the `Axios` dependency to be dynamically imported when the button `Search for GitHub users` is clicked.

When the response is returned, we map through it and display the `usernames` of five users whose names correspond with the search query entered in the input field.
 The GIF below demonstrates the above code block in action:

![Dynamic%20imports%20and%20code%20splitting%20with%20Next%20js%208adec507c38b4d51be25a3f54fb14773/dynamically-importing-library.gif](Dynamic%20imports%20and%20code%20splitting%20with%20Next%20js%208adec507c38b4d51be25a3f54fb14773/dynamically-importing-library.gif)

## Conclusion

We learned about dynamic imports/code splitting, its advantages, and how to use it in a Next.js application in this article.

Overall, if you want to shorten the time it takes for your website to load, dynamic imports and code splitting are a must-have approach. Dynamic imports will significantly enhance the performance and user experience of your website if it has images, or if the results to be shown are dependent on user interactions.

## [LogRocket](https://logrocket.com/signup): Full visibility into production Next.js apps

Debugging Next applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking state, automatically surfacing JavaScript errors, and tracking slow network requests and component load time, [try LogRocket](https://logrocket.com/signup).

![Dynamic%20imports%20and%20code%20splitting%20with%20Next%20js%208adec507c38b4d51be25a3f54fb14773/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png](Dynamic%20imports%20and%20code%20splitting%20with%20Next%20js%208adec507c38b4d51be25a3f54fb14773/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png)

[LogRocket](https://logrocket.com/signup) is like a DVR for web and mobile apps, recording literally everything that happens on your Next app. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app's performance, reporting with metrics like client CPU load, client memory usage, and more.

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your Next.js apps — [start monitoring for free](https://logrocket.com/signup).