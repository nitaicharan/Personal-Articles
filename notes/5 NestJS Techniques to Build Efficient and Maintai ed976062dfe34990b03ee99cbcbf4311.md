# 5 NestJS Techniques to Build Efficient and Maintainable Apps

Created: May 31, 2022 5:55 AM
Finished: Yes
Finished 📅: May 31, 2022
Source: https://betterprogramming.pub/5-nestjs-techniques-to-build-efficient-and-maintainable-apps-be6bc77e789e
Tags: #nestjs

![5%20NestJS%20Techniques%20to%20Build%20Efficient%20and%20Maintai%20ed976062dfe34990b03ee99cbcbf4311/1I00HTH4kt3AFGZuIeT5qng.png](5%20NestJS%20Techniques%20to%20Build%20Efficient%20and%20Maintai%20ed976062dfe34990b03ee99cbcbf4311/1I00HTH4kt3AFGZuIeT5qng.png)

Photo by [Nadi Whatisdelirium](https://unsplash.com/@whatisdelirium?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cat-gem?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

NestJS is a complex framework and evolving continuously. It’s a great framework with many out-of-box features. Some features are not-well known and under-utilized, but they could be very useful when placed in the right place.

In this article, I’m going to walk through 5 hidden gems.

# Versioning

Versioning is essential for web API development. We often need to create a new API version for a breaking change, while keeping the old version working.

Prior to NestJS 8, there was no out-of-box support for API versioning. Thus you will need to implement the versioning manually.

Introduced in NestJS 8, 3 different types of versioning are supported.

- URI versioning- The version will be passed within the URI of the request (default)
- Header versioning- A custom request header will specify the version
- Media Type versioning- The Accept header of the request will specify the version

Let’s use the default URI versioning as an example.

To enable URI Versioning, do the following:

To apply URL versioning to the controller:

```tsx
@Controller({
  version: '1',
})
export class CatsControllerV1 { ....
```

Or you can apply it to individual routes

```tsx
@Version('2')
  @Get('cats')
  findAllV2(): string {
    return 'This action returns all cats for version 2';
  }
```

Another useful feature is the Version “[Neutral](https://docs.nestjs.com/techniques/versioning)”. You can set the controller to be version-neutral:

```tsx
@Controller({
  version: VERSION_NEUTRAL,
})
export class CatsController {
```

If an incoming request does not contain a version at all, it will be mapped to the `VERSION_NEUTRAL` controller.

The new versioning feature in NestJS is simple and flexible. It gives another good reason to use NestJS.

# Use in-memory caching with a custom decorator

Caching is one of the hard problems in Computer Science. NestJs provides an out-of-box c[ache manager](https://docs.nestjs.com/techniques/caching) with an in-memory data store as default.

It’s straightforward to use. Firstly import the `CacheModule.`

Then we can inject it into a class using the `CACHE_MANAGER` token and start to use it.

```
// To retrieve items from the cache
await this.cacheManager.get('key');// To add an item to the cache
await this.cacheManager.set('key', 'value');
```

To implement caching in NestJS API, the code is typically like the following.

When the application grows, the code with the above pattern will be repeated everywhere.

How to reduce duplication? The answer is a [custom decorator](https://docs.nestjs.com/custom-decorators).

A decorator is a function that can extend the behavior of another function without modifying it. To create a decorator that can interact with the cache manager, we need to inject the cache manager into the decorator. The powerful [NestJS DI](https://docs.nestjs.com/fundamentals/custom-providers) makes it relatively easier.

Below is the implementation of a decorator that uses the in-memory cache manager.

The gist of the solution is:

- `target` is the class that contains the decorator
- `descriptor` is the reference to the class method being decorated
- In line 12, we extract the target constructor name and method name to form a `cacheKey`, so it will be unique
- `ttl`is the duration to invalidate the cache, we set the default to 10 seconds
- Line 4 and line 10 are equivalent to calling the @Inject decorator in the constructor as follows:

To use the decorator, is as simple as:

It is so much cleaner now, and we can apply it to any service that needs to be cached.

# NestJS Cron Job

Although NestJS is primarily designed for REST API, it also comes with a scheduling package. The package exposes an API that allows us to create cron jobs.

To get started with the NestJS cron job, we need to install the package first

```
$ npm install --save @nestjs/schedule
$ npm install --save-dev @types/cron
```

You can create a cron job in two different ways:

- create the cron job in a declarative way.

The `@Cron` decorator supports [cron pattern](http://crontab.org/).

```
@Cron('45 * * * * *')
  handleCron() {
    this.logger.debug('Called when the current second is 45');
  }
```

The above `handleCron` method will be called every minute, at the 45th second. You can also use the predefined cron expression to set up the job.

- dynamically create a new cron job.

Using the `CronJob` object from the `cron` package, you can create the cron job. Then you can use the `SchedulerRegistry.addCronJob()` method to add the job to the schedule registry.

```
addCronJob(name: string, seconds: string) {
  const job = new CronJob(`${seconds} * * * * *`, () => {
    this.logger.warn(`time (${seconds}) for job ${name} to run!`);
  });

this.schedulerRegistry.addCronJob(name, job);
job.start();
```

To find out other options and features available in the NestJS task scheduling package, check out the [official documentations](https://docs.nestjs.com/techniques/task-scheduling).

# Validate input with pipes

Data validation is important for any Web API. In NestJS, we can use [pipes](https://docs.nestjs.com/pipes) to perform validation at run time. When a pipe is used for validation, it will return data unchanged if validation is successful, or an error will be thrown if the data is incorrect.

NestJS comes with built-in out of box pipes, one of them is `ValidationPipe`. The built-in ValidationPipe is based on the popular [class-validator](https://github.com/typestack/class-validator) package. An example usage is shown below:

- `@IsString()`: whether an incoming value is a string,
- `@Length(10)`: verify whether the value has a minimum length of 10 characters. It will also return an error if the value is not a string.

When a request is received with an invalid property in the request body `CreateCatDto`, the app will automatically respond with a `400 Bad Request`.

In the above example, the validation is applied with decorators. The same decorator can be used in different DTO classes across the whole NestJS App. It makes the validation logic easier to maintain.

There are a lot more decorators available from the built-in validation pipe. You can also create your own custom validation pipe on top of the [class-transformer](https://github.com/typestack/class-transformer) and class-validator packages.

Pipes in NestJS are flexible and powerful. It can be synchronous and asynchronous. It can also be applied in different levels of scopes: parameter, method, controller, or global.

For more details on how to use NestJS pipes for validation, you can check out the [official documentation](https://docs.nestjs.com/pipes).

# Improve performance by switching underlying platform

By default, NestJS runs on top of Express. Compared with other API frameworks, its performance isn’t the best. For a normal app, the difference in performance won’t be noticed.

But if you are working on an App where very fast performance is the top priority, you can migrate your NestJS App to use F[astify](https://docs.nestjs.com/techniques/performance). Fastify is much faster than Express, approximately two times quicker.

The migration from Express to Fastify is relatively easy, as NestJS provides framework independence based on adapter patterns. In other words, the design of NestJS makes Express and Fastify interchangeable.

To use Fastify, we need to install the package

```
npm i --save @nestjs/platform-fastify
```

Then we can configure the Fastify platform in `main.ts.`

```
async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter()
  );
  await app.listen(3000);
}
```

Now Fastify is ready to go!

Please note that Fastify is used as the HTTP provider in NestJS. Thus, those packages dependent on Express will need to be replaced by Fastify equivalent packages.

In this article, we have walked through 5 less well-known features and their use cases. I hope you learned a thing or two.