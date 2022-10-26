# Circular Dependency

Created: June 4, 2022 10:10 PM
Finished: No
Source: https://docs.nestjs.com/fundamentals/circular-dependency
Tags: #nestjs

A circular dependency occurs when two classes depend on each other. For example, class A needs class B, and class B also needs class A. Circular dependencies can arise in Nest between modules and between providers.

While circular dependencies should be avoided where possible, you can't always do so. In such cases, Nest enables resolving circular dependencies between providers in two ways. In this chapter, we describe using **forward referencing** as one technique, and using the **ModuleRef** class to retrieve a provider instance from the DI container as another.

We also describe resolving circular dependencies between modules.

> Warning
> 
> 
> ```
> cats/cats.controller
> ```
> 
> ```
> cats
> ```
> 
> ```
> cats/cats.service
> ```
> 
> [this github issue](https://github.com/nestjs/nest/issues/1181#issuecomment-430197191)
> 

### Forward reference

A **forward reference** allows Nest to reference classes which aren't yet defined using the `forwardRef()` utility function. For example, if `CatsService` and `CommonService` depend on each other, both sides of the relationship can use `@Inject()` and the `forwardRef()` utility to resolve the circular dependency. Otherwise Nest won't instantiate them because all of the essential metadata won't be available. Here's an example:

```

@Injectable()
export class CatsService {
  constructor(
    @Inject(forwardRef(() => CommonService))
    private commonService: CommonService,
  ) {}
}

```

> Hint
> 
> 
> ```
> forwardRef()
> ```
> 
> ```
> @nestjs/common
> ```
> 

That covers one side of the relationship. Now let's do the same with `CommonService`:

```

@Injectable()
export class CommonService {
  constructor(
    @Inject(forwardRef(() => CatsService))
    private catsService: CatsService,
  ) {}
}

```

> Warning
> 
> 
> ```
> Scope.REQUEST
> ```
> 
> [here](https://github.com/nestjs/nest/issues/5778)
> 

### ModuleRef class alternative

An alternative to using `forwardRef()` is to refactor your code and use the `ModuleRef` class to retrieve a provider on one side of the (otherwise) circular relationship. Learn more about the `ModuleRef` utility class [here](https://docs.nestjs.com/fundamentals/module-ref).

### Module forward reference

In order to resolve circular dependencies between modules, use the same `forwardRef()` utility function on both sides of the modules association. For example:

```

@Module({
  imports: [forwardRef(() => CatsModule)],
})
export class CommonModule {}

```

### Support us

Nest is an MIT-licensed open source project. It can grow thanks to the support by these awesome people. If you'd like to join them, please read more [here](https://docs.nestjs.com/support).

### Join our Newsletter

Subscribe to stay up to date with the latest Nest updates, features, and videos!