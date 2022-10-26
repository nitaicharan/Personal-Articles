# Clean architecture with Nestjs

Created: September 22, 2022 10:52 AM
Finished: No
Source: https://medium.com/@jonathan.pretre91/clean-architecture-with-nestjs-e089cef65045
Tags: #nestjs

# 1- Introduction

What is clean architecture?

Clean architecture was created by Robert C. Martin. He is the author of a few books where you can understand his concept. But in our case, we will keep the focus on **Clean Architecture.**

> Clean architecture is a software design philosophy that separates the elements of a design into ring levels. An important goal of clean architecture is to provide developers with a way to organize code in such a way that it encapsulates the business logic but keeps it separate from the delivery mechanism.
> 

![Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/12nqUx2LoWvC2sK91HVZcFQ.png](Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/12nqUx2LoWvC2sK91HVZcFQ.png)

And I add the code design principles (SOLID) because it is an important part of clean architecture.

- **Single responsibility principle**: a class should have one, and only one, reason to change. Or the new version: a module should be responsible to one, and only one, actor.
- **Open-closed principle**: a class should be open for extension but closed for modification.
- **Liskov’s substitution principle**: objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program.
- **Interface segregation principle**: many client-specific interfaces are better than one general-purpose interface.
- **Dependency inversion principle**: one should depend upon abstractions, not concretions.

What is NestJs?

NestJS is a framework for building efficient and scalable Node.js server-side applications built with and fully supporting TypeScript. It uses robust HTTP Server frameworks like Express or Fastify.

I won’t introduce more about Nestjs and I recommend you to visit the [website](https://docs.nestjs.com/), Nestjs documentation is really huge and complete.

Why did I choose this framework and architecture?

About the framework, I liked the possibility of using a framework made for typescript and the documentation is really completed. But after my first project with this framework, I realized that the common architecture from the documentation was not the best choice. Of course, at the beginning everything is fine, the problem arrive always after some months when the specs change again and again. For that reason, I tried a few architectures and stoped on clean architecture. Clean architecture gives me a solution to my problem.

- How to make a code robust and easy to maintain.
- How to dispatch the work in a team.
- How to work even if you don’t have all spec, database, etc…

# 2- Preparation

Install nest CLI if you don’t have it already and create a new project

```
nest new project-name
```

![Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/1ETZn4kQhQwMQhYexz8YJOg.png](Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/1ETZn4kQhQwMQhYexz8YJOg.png)

Remove app.service.ts and app.controller.ts

Concretely, there are 3 main packages: domain, usecases and infrastructure. These packages have to respect these rules:

- domain contains the business code and its logic and has no outward dependency: nor on frameworks (NestJS in our case), nor on use_cases or infrastructure packages.
- usecases is like a conductor. It will depend only on domain package to execute business logic. use_cases should not have any dependencies on infrastructure (including framework or npm module).
- infrastructure contains all the technical details, configuration, implementations (database, web services, npm module, etc.), and must not contain any business logic. infrastructure has dependencies on domain, use_cases and frameworks.

![Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/1-Fl7km1XeVJhL4tZcevcoA.png](Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/1-Fl7km1XeVJhL4tZcevcoA.png)

The 3 folders

# 3- Coding

For this example, we will realize a basic todo list. I will be brief for many points because it is not essential and/or you can find better information directly inside Nestjs Doc. I will be more focused on the clean architecture principle.

# Configuration

Run the cmd to generate a new module and service. And don’t forget to install `@nestjs/config`

```
nest g mo /infrastructure/config/environment-config
nest g s /infrastructure/config/environment-config
```

Create an interface inside the folder domain.

And implement this interface inside the environment-config service.

Now we will implement Typeorm to be able to access the database.

Don’t forget to install`@nestjs/typeorm`

```
nest g mo /infrastructure/config/typeorm
```

And change the **start:dev** in your package.json

```
"start:dev": "NODE_ENV=local nest start --watch",
```

# Entity

The entities files are inside the infrastructure folder. The entity is a part of the infrastructure for the only reason it using the externals module.

Because the entities are inside the infrastructure, we need a model.

In our case, we don’t need a constructor for this model, but it is a good place to modify your model.

# Logger

All applications are using a logger, there are plenty of loggers, for example, cloud providers (AWS, GCP, Azure, …)use different loggers. Moreover, change your logger can be painful. It is for this reason, I‘m using a module with an interface to abstract this problem to my logic and be no dependant on the cloud provider or an npm module.

```
nest g mo /infrastructure/logger
nest g s /infrastructure/logger
```

This is the interface inside the domain folder.

And the service. In the example, I’m using nest logger but with the abstraction, I can change easily in the future if it is needed.

# Exceptions

The logic is the same as the logger module. To avoid exception errors related to a specific Provider or Nodejs framework. I abstract the service.

```
nest g mo infrastructure/exceptions
nest g s infrastructure/exceptions
```

This is the interface inside the domain folder.

The service is using Nestjs exception.

# Repositories

First, we will need an interface for our request. This interface will be important to abstract the repository. And If in the future you will want to change your ORM or your DB, you will not get problems with your logic.

Now we need a module for our repository.

```
nest g mo /infrastructure/repositories
```

Create a file todo.repository.ts and implement the interface.

> If you use VsCode you can get a helper to implement all your methods
> 

![Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/1MFB_X5VBx_f7vUs5QJTs-w.png](Clean%20architecture%20with%20Nestjs%20899a1c728a464391bdda9064b8cfee28/1MFB_X5VBx_f7vUs5QJTs-w.png)

And this is how the repository will look like.

# Filter

A middleware to catch errors in the code. It is not related to the subject and you can find more information on Nestjs. But this is mine.

And don’t forget to add this line in your main.ts

```
app.useGlobalFilters(new AllExceptionFilter(new LoggerService()));
```

# Interceptor

Same as for filter, to understand more about interceptor you can read Nestjs documentation.

The first interceptor it is for return a formatted response and gets a harmony for all response.

The second interceptor is to handle the incoming request with some log.

```
app.useGlobalInterceptors(new ResponseInterceptor());
```

# Module Usecases proxy

This file is the link between your use cases and your infrastructure, you will inject different services needed for your use cases. In this way, it will be easy to change service in the future and you respect the dependency injection (SOLID)

The file can become quiet big, I didn’t find a way to make it shorter and cleaner at the moment.

# Usecases

In my architecture, I usually make one use case for one endpoint. In this example, we have 5 endpoints so we need 5 use cases. Why one use case for one endpoint? For the simple reason, if you have a monolithic code and you want to move on serverless, each use case will be one function (lambda, GCP function, firebase function,…) or if one use case has to be exported to another API ( could be a lambda ) you will copy past your use case, inject the dependency (logger, repository) and the logic will be done.

> Keep in mind the logic have to be a pure typescript and import only module from domain.
> 

This is an example for get todo:

# Controllers

The controllers can be called routes, you call your use cases in these files. The controllers have to keep clear and simple code. It is also here I write my swagger.

# DTO

The DTO (data transfer object) is the input data for the controller, it will be the data displayed on the swagger and the data verified before use. To check the fields I m using **class-validator.**

# Presenters

If the DTO is the input data, the presenter's data is the output data. The Presenter has the role to format the data as the consumer want. Be sure to keep your logic clear and independent of the consumer requirement.

# Conclusion

I didn’t cover all parts of the code in the article but you can find the all code in the repository.

As you can see the code is huge, need time to prepare, and we can think it is overkill for a small example like this one. But with this architecture, everything can be changed easily: new requirement, migration to a new Npm module, new framework or move to serverless.

In the next articles I will cover the test, in another one migrate nestjs to express, it will be an interesting step because I will probably change some part of the code from OOP to Functional Programming. And to finish this series, try to use this architecture for serverless.

Repo: [https://github.com/jonathanPretre/clean-architecture-nestjs](https://github.com/jonathanPretre/clean-architecture-nestjs)