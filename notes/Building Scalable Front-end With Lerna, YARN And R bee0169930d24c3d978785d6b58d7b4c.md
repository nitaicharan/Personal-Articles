# Building Scalable Front-end With Lerna, YARN And React In 60 Minutes

Created: July 5, 2022 10:25 AM
Finished: No
Source: https://www.velotio.com/engineering-blog/scalable-front-end-with-lerna-yarn-react
Tags: #react

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/6196c1bc915d10babaaf6788_Group203820Copy.svg](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/6196c1bc915d10babaaf6788_Group203820Copy.svg)

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/61f274b506f9b035e7645c08_Group20420Copy2012.svg](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/61f274b506f9b035e7645c08_Group20420Copy2012.svg)

Beginnings are often messy. Be it in any IT project, we often see that at a certain point, people look forward to revamping things. With revamping, there comes additional costs and additional time. And those will be a lot costlier if not addressed at the right time to meet the customers’ feature demands. Most things like code organization, reusability, code cleanup, documentation are often left unattended at the beginning only to realize that they hold the key to faster development and ensure quick delivery of requested features in the future, as projects grow into platforms to serve huge numbers of users.

We are going to look at how to write a scalable frontend platform with [React](https://www.velotio.com/engineering-blog/inline-function-performance-in-react-applications) and Lerna within an hour in this blog post. The goal is to have an organized modular architecture that makes it easy to maintain existing products and can quickly deliver new modules as they arrive.

### **Going Mono-Repo:**

Most projects start with a single git/bitbucket repository and end up in chaos with time. With mono-repo, we can make it more manageable. That being said, we will use Lerna, npm and YARN to initialize our monorepo.

### **Prerequisite**:

npm, npx

Installing YARN: [Installation Guide](https://classic.yarnpkg.com/en/docs/install)

Installing npx

Installing Lerna

After this, we will have Lerna installed globally.

Initializing a project with Lerna.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157565607615c1d7ac931_rFQd_HfX4tT10r7vdTJZshTH0GHM19JFUz_u2jC6op6F4YLn-oI2m_WsGoFK3rjrKtykNstcctlxoDZ_zM8JIa04E4f7dXqi5PxNFTgc-uNG47L0vvBsVZKuy0U8S7MH9M4xHyKw.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157565607615c1d7ac931_rFQd_HfX4tT10r7vdTJZshTH0GHM19JFUz_u2jC6op6F4YLn-oI2m_WsGoFK3rjrKtykNstcctlxoDZ_zM8JIa04E4f7dXqi5PxNFTgc-uNG47L0vvBsVZKuy0U8S7MH9M4xHyKw.png)

Let's go through the files generated with it. So we have the following files:

- package.json
- lerna.json
- packages/

*package.json* is the same as any other npm package.json file. It specifies the name of the project and some basic stuff that we normally define like adding husky for pre-commit hooks.

*lerna.json* is a configuration file for configuring Lerna. You can find more about Lerna configuration and supported options at [Lerna concepts](https://github.com/lerna/lerna/blob/master/README.md#concepts).

*packages/* is a directory where all our modules will be defined and Lerna will take care of referencing them in each other. Lerna will simply create symlinks of referenced local modules to make it available for other modules to use.

To understand Lerna and its concepts, you can go through its [official documentation](https://github.com/lerna/lerna/blob/master/README.md#getting-started).

For better performance, we will go with YARN. So how do we configure YARN with Lerna?

It's pretty simple as shown below.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb15757b471e6fddbeb16a3_kbrxh8ufYYfUsLROIi9VgapjgOD8jbjo-ZmqiU8R-alIQvXsdC7OLo-oI8I3i0bAkc51JUhSCIm4fZm9iOYzqCQwcJpO55dgLTQhnJXxDpu9zZrayUVvIRQkzxYcZgywYKyyjAtX.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb15757b471e6fddbeb16a3_kbrxh8ufYYfUsLROIi9VgapjgOD8jbjo-ZmqiU8R-alIQvXsdC7OLo-oI8I3i0bAkc51JUhSCIm4fZm9iOYzqCQwcJpO55dgLTQhnJXxDpu9zZrayUVvIRQkzxYcZgywYKyyjAtX.png)

We just need to add “npmClient”: “yarn” to lerna.json

### **Using YARN workspaces with Lerna**

YARN workspace is a quick way to get around the mess of `yarn link` i.e. referencing one module into another. Lerna already provides it so why go with YARN workspaces?

The answer is excellent bootstrapping time provided by YARN workspaces.

Here is how we can use YARN workspaces with Lerna

Now we just need to add “useWorkspaces”: true to lerna.json and in package.json, we need to add

This will take care of linking different modules in a UI platform which are mentioned in the packages folder.

Once this is done, we can proceed to bootstrap, which in other terms, means forcefully telling Lerna to link the packages. As of now, we do not have anything under it, but we can run it to check if this setup can bootstrap and work properly.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb1575469abf662bee50e1d_r-OSQz0nt4rSlYJ22q3OT6IR7-rN0yM8jim8FbqmJhocAebYKYO2mB90_vcHSUFuBJ8dgSLAC-sPmwaFCcITn91yptJMRxBIbObdooxutcN0iirll5-FKMSrEmZqE0TrQbn4V0lL.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb1575469abf662bee50e1d_r-OSQz0nt4rSlYJ22q3OT6IR7-rN0yM8jim8FbqmJhocAebYKYO2mB90_vcHSUFuBJ8dgSLAC-sPmwaFCcITn91yptJMRxBIbObdooxutcN0iirll5-FKMSrEmZqE0TrQbn4V0lL.png)

So it's all about the Lerna and YARN set up, but how should one really organize a UI package to build in a manageable and modular way.

### **What most React projects are made of**

1 - Component Libraries
2 - Modules
3 - Utils libraries
4 - Abstractions over React-Redux ( Optional but most organizations go with it)

**Components**: Most organizations end up building their own component libraries and it is crucial to have it separated from the codebase for reusability of components. There exist a lot of libraries, but when you start building the product, you realize every library has something and misses something. Most commonly available libraries convey a standard UX design pattern. What if your designs don’t fit into those at a later point? So we need a separate components library where we can maintain organization-specific components.

**Modules**: At first you may have one module or product, but over time it will grow. To avoid breaking existing modules over time and keeping change lists smaller and limited to individual products, it's essential to split a monolith into multiple modules to manage the chaos of each module within it without impacting other stable modules.

**Utils**: These are common to any project. Almost every one of us ends up creating utils folders in projects to help with little functions like converting currency or converting large numbers like 100000 as 100K and many more. Most of these functions are common and specific to organizations. E.g. a company working with statistics is going to have a helper function to convert large numbers into human-readable figures and eventually they end up copying the same code. Keeping utils separated gives us a unique opportunity to avoid code duplication of such cases and keep consistency across different modules.

**Abstractions over React-Redux**: A lot of organizations prefer to do it. AWS, Microsoft Outlook, and many more have already adopted this strategy to abstract React & Redux bindings to create their own simplified functions to quickly bootstrap a new module/product into an existing ecosystem. This helps in faster delivery of new modules since developers don’t get into the same problems of bootstrapping and can focus on product problems rather than setting up the environment.

One of the most simplified approaches is presented at [react-redux-patch](https://github.com/saurabhnemade/react-redux-patch) to reduce boilerplate. We will not go into depth in this article since it's a vast topic and a lot of people have their opinion on how this should be built.

**Example**:

We will use create-react-app & create-react-library to create a base for our libraries and modules.

Installing create-react-library globally.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157587c1a6c96ddd535c1_ot1tVdEOyU1UUp2LMKvTwamgqJrTmW-hKruzAoDSR6DxfV_24tOhu54HlS4XfXwsvs_wTvjSVDPNqbDPEAoPEHT_62zw3Rfl1gfs2hDHQVXN9LVAfdmqyfT8PChRm_0JA7qR9w3E.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157587c1a6c96ddd535c1_ot1tVdEOyU1UUp2LMKvTwamgqJrTmW-hKruzAoDSR6DxfV_24tOhu54HlS4XfXwsvs_wTvjSVDPNqbDPEAoPEHT_62zw3Rfl1gfs2hDHQVXN9LVAfdmqyfT8PChRm_0JA7qR9w3E.png)

Creating a components library:

create-react-library takes away the pain of complex configurations and enables us to create components and utility libraries with ease.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157585fb51e615af3a1cc_PSvHhOq9KsCt5GmYjUuRlI9Xr8V4TCssFetN77q4vevQ1YfLTrnZW3YRk8COc4JgLv6j-y5MnZ94s7PdtqD1lmbJ83kdGz7GGBnR5mQ6y3RNUIzHco4PEztK8hVgAJewno9b1DGW.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157585fb51e615af3a1cc_PSvHhOq9KsCt5GmYjUuRlI9Xr8V4TCssFetN77q4vevQ1YfLTrnZW3YRk8COc4JgLv6j-y5MnZ94s7PdtqD1lmbJ83kdGz7GGBnR5mQ6y3RNUIzHco4PEztK8hVgAJewno9b1DGW.png)

For starters, just create a simple button component in the library.

Similarly, we can create common-utils packages with the help of create-react-library.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb15758b471e6044beb1744_kPoo_2yv1oRwDXUCAYU1WnkNoNmnNu-nzUKtbbVlJjlaHOOKAjYSASp0riS1kGR_oCYxcBYsYOtCvm6R6c2SpV9NAVp9si6odUuq4JjuFOohH7lrct0RMDfYDpRjhfzRp5tuY6QD.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb15758b471e6044beb1744_kPoo_2yv1oRwDXUCAYU1WnkNoNmnNu-nzUKtbbVlJjlaHOOKAjYSASp0riS1kGR_oCYxcBYsYOtCvm6R6c2SpV9NAVp9si6odUuq4JjuFOohH7lrct0RMDfYDpRjhfzRp5tuY6QD.png)

We will just define a simple formatDate function in the common-utils library.

Now these two packages, ui-components and common-utils are ready to be used in multiple projects.

Let’s create a simple React app with create-react-app.

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb15757326520d4efc4ab26_PcF_zMl_mDqEdqkzpXmi5SEz7PI1omU7nc5hnhc35-LsKo3QQZ9a2qOiMun_Cvg-ZHffK6JuATxya1SY2t-QPjmGFz74eTOPSHXFy3GW-GJMnSjM_rusIC0gRkCU4NnywGPy_Q_W.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb15757326520d4efc4ab26_PcF_zMl_mDqEdqkzpXmi5SEz7PI1omU7nc5hnhc35-LsKo3QQZ9a2qOiMun_Cvg-ZHffK6JuATxya1SY2t-QPjmGFz74eTOPSHXFy3GW-GJMnSjM_rusIC0gRkCU4NnywGPy_Q_W.png)

To link these libraries, simply run

whenever you add a new library to package.json

And we will be ready to use them.

Let’s add the dependencies in package.json

Let's do Lerna bootstrap:

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb1575819a95b7b34d0a9a2_VOxW2Lr8ztFWnCZj7usuFKRg0Awz51Z76DnZwexQTxCuCTvjKy-hMJcFLVw96XqgTsWn9WADUHaPIv_peRjP7-8j7lTs4xlOoH0XbD9FiF5rfOHJH4T4vAtBkRtCyOaKvwYYJ_hk.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb1575819a95b7b34d0a9a2_VOxW2Lr8ztFWnCZj7usuFKRg0Awz51Z76DnZwexQTxCuCTvjKy-hMJcFLVw96XqgTsWn9WADUHaPIv_peRjP7-8j7lTs4xlOoH0XbD9FiF5rfOHJH4T4vAtBkRtCyOaKvwYYJ_hk.png)

Here is a simple example of how we can use it.

### **Real manageable chaos:**

With this strategy, our codebase is now split, can be easily reused, and is modular.

- Every module is placed under packages.
- Product codebase is separated from utils and components
- Multiple teams can work on individual modules
- Finding impact analysis is easier than a monolith project
- Side effects are mostly limited to only one module for small changes

![Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157571377f616b13895f2_sRq3nb_d1ZX5KSA4hTrlxRvwI_5awZHEeAYsFWlmnybhSooD5W_IqK50hesjlkS8XnjrtycKK_8FgyjiEe9KJcEzxezzVFyxXB32JaydWBr4t5frxqDUqzKC-7acz2dRSltTnqbe.png](Building%20Scalable%20Front-end%20With%20Lerna,%20YARN%20And%20R%20bee0169930d24c3d978785d6b58d7b4c/5eb157571377f616b13895f2_sRq3nb_d1ZX5KSA4hTrlxRvwI_5awZHEeAYsFWlmnybhSooD5W_IqK50hesjlkS8XnjrtycKK_8FgyjiEe9KJcEzxezzVFyxXB32JaydWBr4t5frxqDUqzKC-7acz2dRSltTnqbe.png)

All the code in this article is available at [lerna-yarn-react-example](https://github.com/saurabhnemade/lerna-yarn-react-example)

**How about publishing the packages?**

Publishing your packages to a private npm directory is a chaotic thing in monorepo. Lerna provides a very simple approach towards publishing packages.

You will just need to add the following to package.json

Now you can simply run

It will try to publish the packages that have been changed since the last commit.

**Summary**:

With the use of Lerna and YARN, we can create an efficient front-end architecture to quickly deliver new features with less impact on existing modules. Of course with additional bootstrapping tools like yeoman generator along with abstractions over React and Redux, it makes the process of introducing to new modules a piece of a cake. Over time, you can easily split these modules and components into individual repositories by utilizing the private npm repositories. But for the initial chaos of getting things working and quick prototyping of your next big company’s UI architecture, Lerna and YARN are perfectly suited tools!!!

[Saurabh is Technical Lead at Velotio. He is Full stack engineer with strong focus on security principles. He is having keen interest in solving puzzles, Rubik cubes and building unique software ideas.](https://www.velotio.com/author/saurabh-nemade)