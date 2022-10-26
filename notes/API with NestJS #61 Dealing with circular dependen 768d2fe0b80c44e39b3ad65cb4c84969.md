# API with NestJS #61. Dealing with circular dependencies

Created: June 4, 2022 10:01 PM
Finished: Yes
Finished 📅: June 4, 2022
Source: https://wanago.io/2022/02/28/api-nestjs-circular-dependencies/
Tags: #nestjs

# 

![API%20with%20NestJS%20#61%20Dealing%20with%20circular%20dependen%20768d2fe0b80c44e39b3ad65cb4c84969/circular.png](API%20with%20NestJS%20#61%20Dealing%20with%20circular%20dependen%20768d2fe0b80c44e39b3ad65cb4c84969/circular.png)

- 33. [API with NestJS #33. Managing PostgreSQL relationships with Prisma](https://wanago.io/2021/04/05/api-nestjs-33-postgresql-relationships-prisma/)
- 49. [API with NestJS #49. Updating with PUT and PATCH with MongoDB and Mongoose](https://wanago.io/2021/09/27/api-nestjs-put-patch-mongodb-mongoose/)
- 50. [API with NestJS #50. Introduction to logging with the built-in logger and TypeORM](https://wanago.io/2021/10/04/api-nestjs-logging-typeorm/)
- 52. [API with NestJS #52. Generating documentation with Compodoc and JSDoc](https://wanago.io/2021/10/18/api-nestjs-documentation-compodoc-jsdoc/)
- 53. [API with NestJS #53. Implementing soft deletes with PostgreSQL and TypeORM](https://wanago.io/2021/10/25/api-nestjs-soft-deletes-postgresql-typeorm/)
- 55. [API with NestJS #55. Uploading files to the server](https://wanago.io/2021/11/08/api-nestjs-uploading-files-to-server/)
- 56. [API with NestJS #56. Authorization with roles and claims](https://wanago.io/2021/11/15/api-nestjs-authorization-roles-claims/)
- 58. [API with NestJS #58. Using ETag to implement cache and save bandwidth](https://wanago.io/2022/01/17/api-nestjs-etag-cache/)
- 59. [API with NestJS #59. Introduction to a monorepo with Lerna and Yarn workspaces](https://wanago.io/2022/01/31/api-nestjs-monorepo-lerna-yarn-workspaces/)
- 61. API with NestJS #61. Dealing with circular dependencies
- 63. [API with NestJS #63. Relationships with PostgreSQL and MikroORM](https://wanago.io/2022/05/30/api-nestjs-relationships-postgresql-mikroorm/)

We need to watch out for quite a few pitfalls when designing our architecture. One of them is the possibility of **circular dependencies**. In this article, we go through this concept in the context of Node.js modules and NestJS services.

## Circular dependencies in Node.js modules

A circular dependency between Node.js modules happens when two files import each other. Let’s look into this straightforward example:

one.js

two.js

After running node ./one.js, we see the following output:

> file two.js: undefined 2 file one.js: 1 2 (node:109498) Warning: Accessing non-existent property ‘one’ of module exports inside circular dependency (Use node --trace-warnings ... to show where the warning was created)
> 

We can see that a code containing circular dependencies can run but might result in unexpected results. The above code executes as follows:

- to prevent an infinite loop, two.js loads an **unfinished copy** of one.js,

The above example allows us to understand how Node.js reacts to circular dependencies. Circular dependencies are often a sign of a bad design, and we should avoid them when possible.

### Detecting circular dependencies using ESLint

The provided code example makes it very obvious that there is a circular dependency between files. It is not always that apparent, though. Fortunately, ESLint can help us detect such dependencies. To do that, we need the [eslint-plugin-import](https://www.npmjs.com/package/eslint-plugin-import) package.

The rule that we want is called import/no-cycle, and it ensures that no circular dependencies are present between our files.

In our NestJS project, we would set the configuration in the following way:

.eslintrc.js

## Circular dependencies in NestJS

Besides circular dependencies between Node.js modules, we might also run into this issue when working with NestJS modules. In [part 55 of this series](https://wanago.io/2021/11/08/api-nestjs-uploading-files-to-server/), we’ve implemented a feature of uploading files to the server. Let’s expand on it to create a case with a circular dependency.

localFiles.service.js

users.service.js

### Solving the issue using forward referencing

In our case, the LocalFilesService needs the UsersService and the other way around. Let’s look into how our modules look so far.

localFiles.module.ts

users.module.ts

Above, we see that LocalFilesModule imports the UsersModule and vice versa. Running the application with the above configuration causes an error, unfortunately.

> [ExceptionHandler] Nest cannot create the LocalFilesModule instance. The module at index [2] of the LocalFilesModule “imports” array is undefined.
> 
> 
> Potential causes: – A circular dependency between modules. Use forwardRef() to avoid it. Read more: https://docs.nestjs.com/fundamentals/circular-dependency – The module at index [2] is of type “undefined”. Check your import statements and the type of the module.
> 
> Scope [AppModule -> PostsModule -> UsersModule] Error: Nest cannot create the LocalFilesModule instance. The module at index [2] of the LocalFilesModule “imports” array is undefined.
> 

A *workaround* for the above is to use **forward referencing**. Thanks to it, we can refer to a module before NestJS initializes it. To do that, we need to use the forwardRef function.

localFiles.module.ts

users.module.ts

Doing the above solves the issue of circular dependencies between our modules. Unfortunately, we still need to fix the problem for services. We need to use the forwardRef function and the @Inject() decorator to do that.

localFiles.service.ts

users.service.ts

Doing all of the above causes our services to function correctly despite the circular dependencies.

### Circular dependencies between TypeORM entities

We might also run into issues with circular dependencies with TypeORM entities. For example, this might happen when dealing with relationships.

> If you want to know more about relationships with TypeORM and NestJS, check out API with NestJS #7. Creating relationships with Postgres and TypeORM
> 

Fortunately, people noticed this problem, and there is a solution. For a whole discussion, [check out this issue on GitHub](https://github.com/typeorm/typeorm/issues/4190).

## Avoiding circular dependencies in our architecture

Unfortunately, having circular dependencies in our modules is often a sign of a design worth improving. In our case, we violate the single responsibility principle of SOLID. Our LocalFilesService and UsersService are responsible for multiple functionalities.

> If you want to know more about SOLID, check out Applying SOLID principles to your TypeScript code
> 

We can create a service that encapsulates the functionalities that would otherwise cause the circular dependency issue.

userAvatars.service.ts

We can now use the above service straight in our UsersController or create a brand new controller just for the new service.

users.controller.ts

## Summary

In this article, we’ve looked into the issue of circular dependencies in the context of Node.js and NestJS. We’ve learned how Node.js deals with circular dependencies and how it can lead to problems that might be difficult to predict. We’ve also dealt with circular dependencies across NestJS using forward referencing. Since that is just a workaround and circular dependencies can signify a lacking design, we rewrote our code to eliminate it. That is usually the best approach, and we should avoid introducing circular dependencies to our architecture.