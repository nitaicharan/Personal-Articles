# How We Built a Seamless User-Experience from Multiple Small Front-End Applications

Created: July 5, 2022 10:25 AM
Finished: No
Source: https://www.smartly.io/blog/how-we-built-a-seamless-user-experience-from-multiple-small-front-end-applications

![Smartly-pictures-04.jpg](How%20We%20Built%20a%20Seamless%20User-Experience%20from%20Multi%2059b7be8dab0446959150e569d92f72d6/Smartly-pictures-04.jpg)

In the past five years, our front-end codebase had grown so much that it became unmanageable. Test runs got lengthy and builds took forever to complete. Optimization to our Webpack setups, like [AutoDLL](https://github.com/asfktz/autodll-webpack-plugin) and [Happypack](https://github.com/amireh/happypack), and test suites could only get us so far as more features were piling up and the codebase kept on expanding.

Running tests, type checks, linters and the like would take its sweet time no matter how well we optimized. On the backend side, we already manage the complexity by splitting parts of the application into microservices. As we got around planning a completely new piece of our front-end, Pinterest campaign creation, we decided it was finally time to start splitting our front-end into smaller chunks as well.

### The new front-end architecture

Our baseline was a single, monolithic front-end application built with AngularJS and React over the last five years. We wanted to be able to develop pieces of our front-end application separately while keeping the end-user experience similar to using a single app. These separated packages should have their own development, testing, and release setups, and they should not depend on the old front-end to be able to run locally.

### Monorepo

We chose a [monorepo](https://en.wikipedia.org/wiki/Monorepo) structure to separate the new Pinterest pieces from our old codebase. After [comparing some options](http://rachaelmoore.name/posts/development/devops/comparison-of-monorepo-tools/), we decided to set it up using [Lerna](https://github.com/lerna/lerna). We had good experiences using Lerna in a previous project, where we separated common reusable components from the old front-end into a dedicated design system. Monorepo structure and Lerna enabled us to work effortlessly on multiple packages that *depend on each other*. Lerna also parallelizes builds and test runs for the packages.

Some of the advantages of the new setup:

- We can ignore a lot of the legacy we have in the old frontend.
- We do not need to support AngularJS and related tooling.
- We can go fully TypeScript and not support Flow typing.
- We can keep our conventions better in check.
- Test runs and type checks are insanely fast compared to the old monolithic front-end.
- Making sense of the whole codebase is much much easier.

### Local development

While we do use [React Storybook](https://storybook.js.org/) with our design system component library, we did not take it into use with our new Pinterest monorepo. Storybook is a quick way of testing individual components, which is why we like to use it most of the time. However, the Pinterest front-end parts are full, small applications with routing, Redux setup, they access APIs, and the like, which makes them less suited for Storybook.

Instead, we set up a small testbench for running the application locally during development, built and served by the [Webpack Dev Server](https://webpack.js.org/configuration/dev-server/). The testbench application is simply a React application with a router with each route pointing to a separate package within the monorepo. Those packages are lazy-loaded into the testbench much like they would be, were they integrated into the old front-end.

### Routing

When integrating sub-applications to our main app routing becomes an additional problem. Our goal is to let the child application define its own routes, but at the same time, we want to allow the parent app to mount the child app into any path, as well as change that path at will. We also want all the links in our child app to be correctly prefixed with this mount path. This means that our child application routes (as well as links) need to be dynamic and agnostic to the parent application’s routing implementation.

In our case, we use React Router to build the child application routes. Meanwhile, our parent application is using a mix of Angular router and Redux-first Router. By default, React Router’s Router (or BrowserRouter) components do not allow defining a custom prefix to all of the child application routes. Using the ‘basename’ prop does not solve the problem since it prevents us from navigating outside the basename path.

Our solution to this issue is to store the *history* instance created by Redux First Router’s *connectRoutes* function in the parent app. In the sub-application, instead of using the BrowserRouter component from React Router package, we would use the plain Router that accepts an instance of history as a prop. The main frontend application’s history would be passed into the sub-application and used as the history for that router. To accommodate the router for the basename, under which the sub-application will be mounted, the history will be extended by the sub-application as you can see here:

This allows the React Router’s Link and NavLink component to automatically build links using the provided dynamic prefix without having to define them in the child app statically. We also preserve the existing history stack for back navigation without having to use a blank history in the child app. Additionally, we now use the basename definition when creating the route map when the application is initialized:

Using the same instance of history in the child application solves the following problems:

- The browser’s back button now behaves in a consistent manner.
- Prefixing routes in the child applications.
- Prefixing links in React Router without needing to pass a string variable throughout the application.
- The existing history from the parent application’s side remains and changes from the child application are reflected on the same history.

### The workflow of releasing separated packages as one front-end application

1. The developer would work on the changes to the individual front-end package.
2. A PR is opened to the monorepo, which triggers automated test runs for the package.
3. When tests are passed, and the solution is peer-reviewed, the code is merged to monorepo master.
4. Merging to master triggers a release to our private NPM equivalent. Lerna handles building and releasing the various changed packages.
5. A PR for upgrading the package is opened to the main front-end repository.
6. Automated integration tests are run. It is a much lighter test setup than the full extent of the tests within the monorepo package.
7. Once everything is green, the PR can be merged which then triggers an automated release to production.
8. The release is built in a way that the child applications can be lazy-loaded when needed. Webpack can optimize chunks of dependencies.

### Conclusion

Progress on the front-end application has been swift. Without the weight of the old front-end, Pinterest tools build quickly and update automatically almost quicker than it takes to switch between the editor and the browser. However, the mocks can also cause issues when the GraphQL types in the production front-end are ahead of those implemented on the backend side. This requires some coordination between the back-end and front-end development.

### **See also:**

My colleague Mikko Kivelä gave a talk on this topic at the React Helsinki meetup yesterday. Find the talk embedded below and check out the example project he uses here: [https://github.com/smartlyio/micro-frontend-starter](https://github.com/smartlyio/micro-frontend-starter).

Learn more about how developers work at Smartly.io: [www.smartly.io/careers/engineering](https://www.smartly.io/careers/engineering).