# Preact vs. React: A Quick Comparison

Created: July 5, 2022 10:20 AM
Finished: No
Source: https://hackernoon.com/major-differences-between-preact-and-react-which-one-should-you-use
Tags: #react

[Preact%20vs%20React%20A%20Quick%20Comparison%203bd936f5cbea463bbfb74dc9e9d9fc32/image](Preact%20vs%20React%20A%20Quick%20Comparison%203bd936f5cbea463bbfb74dc9e9d9fc32/image)

A versatile leader with experience in assisting successful companies extend their tech capabilities.

React is undoubtedly reigning in the IT industry with its rich features and functionalities. It has revamped the way web development takes place. According to [Stackoverflow Developer Survey 2021](https://insights.stackoverflow.com/survey/2021?ref=hackernoon.com#most-loved-dreaded-and-wanted-webframe-want), React has grabbed the first position for the most wanted framework to build web apps.

But, what is Preact? Have you ever heard of Preact? This technology is claimed to be a lightweight alternative to React for building web applications. We will walk you through the differences between Preact and React in this blog and shed some light on the pros and cons of both technologies.

## **What is Preact?**

Preact is a JavaScript library, considered the lightweight 3kb alternative of React with the same modern API and ECMA Script support. It is designed to work in a browser with DOM, which justifies why it is so tiny. Moreover, it outperforms in speed and implements the virtual DOM like other JavaScript frameworks. With 30.6K stars on [GitHub](https://github.com/preactjs/preact?ref=hackernoon.com) and over 539,044 weekly downloads on [NPM](https://www.npmjs.com/package/preact?ref=hackernoon.com), we can vouch for the popularity of this JavaScript library.

Use Case Examples: Groupon, Uber, Lyft, Financial Times, Broadway, Domino’s, Etsy, etc.

## Why Preact?

Preact is the best choice if you are concerned about the app’s performance, speed, and size. It is used mainly for building progressive web apps. For instance, [Uber](https://medium.com/dev-channel/treebo-a-react-and-preact-progressive-web-app-performance-case-study-5e4f450d5299?ref=hackernoon.com) has switched its PWA to Preact in production to make it super-lightweight and high-performing.

Apart from this, it’s a very small library, which doesn’t demand extra effort to put in for learning. Moreover, its similarity and compatibility with React make it an easy-to-use library to use with the existing React package, with some aliasing, i.e., using preact/compat.

## What is React?

React is the most popular JavaScript library for building interactive user interfaces. Facebook released it in 2013. With 180K stars on [GitHub](https://github.com/facebook/react?ref=hackernoon.com), React has become the first choice among developers for web app development. A [survey report from Statista](https://www.statista.com/statistics/1124699/worldwide-developer-survey-most-used-frameworks-web/?ref=hackernoon.com)vouches for its excellence and popularity in the web development industry.

Use Case Examples: Facebook, Netflix, Instacart, Dropbox, Salesforce, PayPal, etc.

## Why React?

React is an excellent pick to build complex and highly scalable applications, especially single-page applications. It also offers efficient server-side rendering, making it the best among all [JavaScript frameworks to create apps](https://www.simform.com/blog/javascript-frameworks/?ref=hackernoon.com) that are easy to optimize according to search engines. Moreover, the strong community of developers and contributors worldwide makes it the most preferred framework among developers and businesses for building web applications.

## Pros & Cons

**Pros of Preact**

- Fast & Lightweight: Preact is just 3.5 kb in size and renders quickly, making it the best framework for building high-performing lightweight apps.
- Compatible: It is highly compatible with React API and supports the same ECMA Script, making it efficient enough to integrate with an existing React project for enhanced performance.
- Large Ecosystem: It is well-documented, which makes the learning curve simple. It also has a large community with many contributors - ready to make bug fixes and provide solutions to the problems.
- Dedicated CLI: Preact has a powerful CLI that helps developers build PWA in a few seconds without configuring WebPack, babel, Terser, etc.
- Preact/compat: It has a package named preact/compat to offer 100% compatibility. It let developers use React libraries with Preact in order to write ReactDOM code without making any changes to the workflow and codebase.

**Cons of Preact**

- Mimics React: Preact was developed as a lightweight alternative to React. Hence, most of the development through this library mimics React instead of innovating the app development.
- Use of additional library: With Preact, it is necessary to use additional libraries like preact/compat, preact/test-utils, etc., to bring connectivity between Preact and React-based npm packages. This makes the project large and slow.
- No Synthetic Event Handling: Preact is based on browser API and doesn’t support synthetic event handling, which can affect app performance and cause maintenance issues while using React for development and Preact for production.

**Pros of React**

- One-way Data Binding: React supports unidirectional data binding, making the app less prone to errors and simplifying debugging.
- Well-documented: React has proper documentation with several tutorials and examples that make the learning process easy.
- Large Community: React is backed by a large community of developers and contributors, making it the best framework for web app development.
- Reusable Components: Reusable components let developers segregate the complex UI into several components that can be used in other parts of the application, making the app development fast.
- JSX: React.js supports JSX to write HTML in JavaScript, which helps create UI features efficiently by reflecting the changes in the app in real-time.

**Cons of React**

- Unopinionated Framework: React is an unopinionated framework, making it flexible in defining the architecture. But, it can cause issues if developers don’t have good architecture knowledge.
- View-centric Library: React, being a view-centric library, demands to do some legwork to figure out several aspects like state management, routing, data handling, relying on different third-party modules and libraries like Redux and NPM.

## What’s Different in Preact & React?

Preact and React have some subtle differences:

- **Hooks:** Hooks are stored separately in Preact and need to be imported differently than React.
- App Size: Preact is much smaller in size than React. The Preact 7.2.0 in minified and gzipped is just 3kb while React 16.2.0 is 31.8K in size, as mentioned on .
- **Rendering:** React apps take a long time in loading, making the rendering process slow and complex. Preact, on the other side, has a faster rendering process.
- **Event Handling:** React implements its own synthetic event system for event handling, reducing app performance, while Preact has no synthetic event. Instead, it uses the browser’s addEventListener for performance and size benefits.
- **Use of JSX and HTML:** With Preact, you can use old HTML attributes as it doesn’t push JSX for client-side templating, unlike React.

## What Should You Consider? Preact or React

Preact and React are both open-source JavaScript libraries that you can use. React is used most for its reusable components, while Preact is preferred for performance reasons. But, we can’t say that Preact can replace React. Both have their advantages and disadvantages. We would suggest you pick according to your business objective and development requirements.

However, you can use both libraries or switch between these two as per your requirement. Apart from this, developers are not required to learn an entirely new technology to use Preact, but they should have expertise in React.

React.js tops the market for its rich features, while Preact gives the advantage in lightning-fast performance. So, if you want to develop small applications like landing pages and PWAs that can load fast, then choose Preact. But, you can’t create a complex app alone in Preact. You will have to use React for such complex applications.

L O A D I N G
. . . comments &