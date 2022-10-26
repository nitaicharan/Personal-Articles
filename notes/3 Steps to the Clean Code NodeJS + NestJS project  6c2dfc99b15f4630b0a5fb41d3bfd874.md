# 3 Steps to the Clean Code: NodeJS + NestJS project tips.

Created: June 27, 2022 10:20 AM
Finished: No
Source: https://medium.com/@maksim_smagin/3-steps-to-the-clean-code-nodejs-nestjs-project-tips-5857e0f39610
Tags: #nodejs

![3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1o-EXRh62Vv1wF6hGc8enkg.jpeg](3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1o-EXRh62Vv1wF6hGc8enkg.jpeg)

Photo by [Clément Hélardot](https://unsplash.com/es/@clemhlrdt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/programming-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

When I first started to use NestJS in my projects, I did lots of mistakes, so here I want to share my experience and help other programmers to avoid them.

We going to talk about some architectural things you might want to use, to make your job easier, and code smaller and more elegant. So let's start :)

# 1: Have a unified folder structure

I am sure everyone had an experience when the project structure was just a mess. The project is growing, but with bad folder structure — it’s really hard to keep things clean and organized.

So to help it out, it’s really important to **think about the folder structure of your project — from the beginning**. Here 3 things you need to follow to get better at it:

- try to think of something universal
- try not to overcomplicate things
- try to think of something you can extend easily in future

Here’s an example of what I'm using:

*common* — folder contains optional modules, so it means it’s not necessary to use them in the project.

*core* — essential modules required to run the application.

*modules* — contains our business logic code. And each module also has a strict structure. It's not necessary to have all subfolders, just add them if you need :)

![3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1ZD38lD2UqHCvyHX8ebRs1Q.jpeg](3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1ZD38lD2UqHCvyHX8ebRs1Q.jpeg)

Example: users module structure

This structure meets all 3 criteria:

- **easy to extend** — u just need to create a new module, or a new folder inside for typical code blocks: types, DTOs, entities for database … etc.
- **I would say universal** — no need to think of where to place a newly created class, service … — there’s a folder for it.
- **it's pretty simple** — it has really strait forward structure, and all folders got clear names, so petty easy to navigate in the project.

# 2: Use decorators, it’s really powerful (even if you do like them :)

Ask yourself —are you afraid of the decorators ?) What I mean it’s many programmers literally avoid using the decorators in TypeScript. I think it first of all comes from a lack of understanding, and feel — it’s some kind of magic.

There’s no magic in them… It’s really simple abstraction, which gives us a really powerful tool — **the ability to write code in a declarative way and code reduce**. Here’s a little example:

Im trying to validate `options`object, and if it has a valid email and name to update user information.

Example of a typical way to validate service method arguments

This little check already **took 7 lines of the code** and still does a **pretty basic check**. Imagine if it would be a **bigger** object to check? Of course, we can move it to some private method, or function outside of the class — but we can do much better :)

For example, we can use some library to validate method parameters using schemas and create a decorator `ValidateArgs` to make it way more elegant.

In this article full implementation of the **ValidateArgs** decorator:

As you can see we literally moved away from all validation from the service method to the decorator. And it’s much easier to read, our code got much smaller and cleaner — it’s just one line :)

Example of the usage of ValidateArgs decorator

# 3. Dependency Injection, use it right.

Dependency injection is all about managing the dependencies right?) We use this principle to decouple code into smaller pieces, so they can **depend on each other — less**, and let us reuse it.

![3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1f7ru9hHhZUVpZWeXWxKSJA.png](3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1f7ru9hHhZUVpZWeXWxKSJA.png)

Dependency injection illustration: ClassB injects ClassA

When you first time read NestJS documentation, in all examples you will find dependency injection implemented this way:

![3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1Ev0B2K4HtoUIHSfpJXzYJw.png](3%20Steps%20to%20the%20Clean%20Code%20NodeJS%20+%20NestJS%20project%20%206c2dfc99b15f4630b0a5fb41d3bfd874/1Ev0B2K4HtoUIHSfpJXzYJw.png)

Service injected directly: Without interface

Basically **in all examples services are injected directly**. I think it gives bad advice to the new people who just started. Because this way, it’s really easy to get into the situation which calls — **circular dependency, or unresolved imports**.

**My solution to this — is just to use interfaces for your services, and always do injection by interfaces :)**

Example using Interface

So here we defined the interface UserUseCase — which just describes methods available to be executed for users, and UsersService implements it. Also, I use Symbol to create injection key — UsersUseCaseSymbol, so we can inject an instance of that service later :)

Example of injection UsersService

As you can see, **UsersController knows nothing about UsersService**, it just refers to its interface. So it means as long as *UsersService* implements *UsersUseCase* — we will be fine because there’s just no direct dependency.

The even bigger advantage it gives **then we try to use UsersService in other modules**. Other modules know nothing about its implementation, they just know about the interface *UsersUseCase* :)

PS: for small projects, it might be too much :)

# Enough for today :)

I hope these 3 steps can help someone write a better code :)