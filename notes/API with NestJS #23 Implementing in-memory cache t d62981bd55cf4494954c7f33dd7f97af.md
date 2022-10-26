# API with NestJS #23. Implementing in-memory cache to increase the performance

Course relation: Series%20API%20with%20NestJS%20b9df00d705df4156a80e837561dd0cfc.md
Created: May 26, 2022 11:35 PM
Finished: Yes
Finished 📅: May 27, 2022
Order: 23
Source: https://wanago.io/2021/01/04/api-nestjs-in-memory-cache-performance/
Tags: #nestjs

# 

![API%20with%20NestJS%20#23%20Implementing%20in-memory%20cache%20t%20d62981bd55cf4494954c7f33dd7f97af/nest23.png](API%20with%20NestJS%20#23%20Implementing%20in-memory%20cache%20t%20d62981bd55cf4494954c7f33dd7f97af/nest23.png)

- 1. [API with NestJS #1. Controllers, routing and the module structure](https://wanago.io/2020/05/11/nestjs-api-controllers-routing-module/)
- 2. [API with NestJS #2. Setting up a PostgreSQL database with TypeORM](https://wanago.io/2020/05/18/api-nestjs-postgresql-typeorm/)
- 3. [API with NestJS #3. Authenticating users with bcrypt, Passport, JWT, and cookies](https://wanago.io/2020/05/25/api-nestjs-authenticating-users-bcrypt-passport-jwt-cookies/)
- 4. [API with NestJS #4. Error handling and data validation](https://wanago.io/2020/06/01/api-nestjs-error-handling-validation/)
- 5. [API with NestJS #5. Serializing the response with interceptors](https://wanago.io/2020/06/08/api-nestjs-serializing-response-interceptors/)
- 6. [API with NestJS #6. Looking into dependency injection and modules](https://wanago.io/2020/06/15/api-with-nestjs-6-looking-into-dependency-injection-and-modules/)
- 7. [API with NestJS #7. Creating relationships with Postgres and TypeORM](https://wanago.io/2020/06/22/api-nestjs-relationships-postgres-typeorm/)
- 9. [API with NestJS #9. Testing services and controllers with integration tests](https://wanago.io/2020/07/13/api-nestjs-testing-services-controllers-integration-tests/)
- 10. [API with NestJS #10. Uploading public files to Amazon S3](https://wanago.io/2020/08/03/api-nestjs-uploading-public-files-to-amazon-s3/)
- 11. [API with NestJS #11. Managing private files with Amazon S3](https://wanago.io/2020/08/10/api-nestjs-private-files-amazon-s3/)
- 12. [API with NestJS #12. Introduction to Elasticsearch](https://wanago.io/2020/09/07/api-nestjs-elasticsearch/)
- 13. [API with NestJS #13. Implementing refresh tokens using JWT](https://wanago.io/2020/09/21/api-nestjs-refresh-tokens-jwt/)
- 14. [API with NestJS #14. Improving performance of our Postgres database with indexes](https://wanago.io/2020/10/19/nestjs-performance-postgres-database-indexes/)
- 15. [API with NestJS #15. Defining transactions with PostgreSQL and TypeORM](https://wanago.io/2020/10/26/api-nestjs-transactions-postgresql-typeorm/)
- 16. [API with NestJS #16. Using the array data type with PostgreSQL and TypeORM](https://wanago.io/2020/11/02/api-nestjs-array-data-type-postgresql-typeorm/)
- 17. [API with NestJS #17. Offset and keyset pagination with PostgreSQL and TypeORM](https://wanago.io/2020/11/09/api-nestjs-offset-keyset-pagination-postgresql-typeorm/)
- 18. [API with NestJS #18. Exploring the idea of microservices](https://wanago.io/2020/11/16/api-nestjs-microservices/)
- 19. [API with NestJS #19. Using RabbitMQ to communicate with microservices](https://wanago.io/2020/11/23/api-nestjs-rabbitmq-microservices/)
- 20. [API with NestJS #20. Communicating with microservices using the gRPC framework](https://wanago.io/2020/11/30/api-nestjs-microservices-grpc-framework/)
- 22. [API with NestJS #22. Storing JSON with PostgreSQL and TypeORM](https://wanago.io/2020/12/28/nestjs-json-postgresql-typeorm/)
- 23. API with NestJS #23. Implementing in-memory cache to increase the performance
- 24. [API with NestJS #24. Cache with Redis. Running the app in a Node.js cluster](https://wanago.io/2021/01/11/nestjs-cache-redis-node-js-cluster/)
- 25. [API with NestJS #25. Sending scheduled emails with cron and Nodemailer](https://wanago.io/2021/01/18/api-nestjs-cron-nodemailer/)
- 26. [API with NestJS #26. Real-time chat with WebSockets](https://wanago.io/2021/01/25/api-nestjs-chat-websockets/)
- 27. [API with NestJS #27. Introduction to GraphQL. Queries, mutations, and authentication](https://wanago.io/2021/02/01/api-nestjs-graphql-introduction/)
- 28. [API with NestJS #28. Dealing in the N + 1 problem in GraphQL](https://wanago.io/2021/02/08/api-nestjs-n-1-problem-graphql/)
- 29. [API with NestJS #29. Real-time updates with GraphQL subscriptions](https://wanago.io/2021/02/15/api-nestjs-real-time-graphql-subscriptions/)
- 32. [API with NestJS #32. Introduction to Prisma with PostgreSQL](https://wanago.io/2021/03/29/api-nestjs-prisma-postgresql/)
- 33. [API with NestJS #33. Managing PostgreSQL relationships with Prisma](https://wanago.io/2021/04/05/api-nestjs-33-postgresql-relationships-prisma/)
- 34. [API with NestJS #34. Handling CPU-intensive tasks with queues](https://wanago.io/2021/05/03/api-nestjs-cpu-intensive-tasks-queues/)
- 35. [API with NestJS #35. Using server-side sessions instead of JSON Web Tokens](https://wanago.io/2021/06/07/api-nestjs-server-side-sessions-instead-of-json-web-tokens/)
- 36. [API with NestJS #36. Introduction to Stripe with React](https://wanago.io/2021/06/14/api-nestjs-stripe-react/)
- 37. [API with NestJS #37. Using Stripe to save credit cards for future use](https://wanago.io/2021/06/21/api-nestjs-stripe-save-credit-cards/)
- 38. [API with NestJS #38. Setting up recurring payments via subscriptions with Stripe](https://wanago.io/2021/06/28/api-nestjs-subscriptions-with-stripe/)
- 39. [API with NestJS #39. Reacting to Stripe events with webhooks](https://wanago.io/2021/07/05/api-nestjs-stripe-events-webhooks/)
- 40. [API with NestJS #40. Confirming the email address](https://wanago.io/2021/07/12/api-nestjs-confirming-email/)
- 41. [API with NestJS #41. Verifying phone numbers and sending SMS messages with Twilio](https://wanago.io/2021/07/19/api-nestjs-verifying-phone-numbers-sending-sms-twilio/)
- 42. [API with NestJS #42. Authenticating users with Google](https://wanago.io/2021/07/26/api-nestjs-google-authentication/)
- 43. [API with NestJS #43. Introduction to MongoDB](https://wanago.io/2021/08/16/api-nestjs-mongodb/)
- 44. [API with NestJS #44. Implementing relationships with MongoDB](https://wanago.io/2021/08/23/api-nestjs-relationships-mongodb/)
- 45. [API with NestJS #45. Virtual properties with MongoDB and Mongoose](https://wanago.io/2021/08/30/api-nestjs-virtual-properties-mongodb-mongoose/)
- 46. [API with NestJS #46. Managing transactions with MongoDB and Mongoose](https://wanago.io/2021/09/06/api-nestjs-transactions-mongodb-mongoose/)
- 47. [API with NestJS #47. Implementing pagination with MongoDB and Mongoose](https://wanago.io/2021/09/13/api-nestjs-pagination-mongodb-mongoose/)
- 48. [API with NestJS #48. Definining indexes with MongoDB and Mongoose](https://wanago.io/2021/09/20/api-nestjs-indexes-mongodb-mongoose/)
- 49. [API with NestJS #49. Updating with PUT and PATCH with MongoDB and Mongoose](https://wanago.io/2021/09/27/api-nestjs-put-patch-mongodb-mongoose/)
- 50. [API with NestJS #50. Introduction to logging with the built-in logger and TypeORM](https://wanago.io/2021/10/04/api-nestjs-logging-typeorm/)
- 51. [API with NestJS #51. Health checks with Terminus and Datadog](https://wanago.io/2021/10/11/api-nestjs-health-checks-terminus-datadog/)
- 52. [API with NestJS #52. Generating documentation with Compodoc and JSDoc](https://wanago.io/2021/10/18/api-nestjs-documentation-compodoc-jsdoc/)
- 53. [API with NestJS #53. Implementing soft deletes with PostgreSQL and TypeORM](https://wanago.io/2021/10/25/api-nestjs-soft-deletes-postgresql-typeorm/)
- 54. [API with NestJS #54. Storing files inside a PostgreSQL database](https://wanago.io/2021/11/01/api-nestjs-storing-files-postgresql-database/)
- 55. [API with NestJS #55. Uploading files to the server](https://wanago.io/2021/11/08/api-nestjs-uploading-files-to-server/)
- 56. [API with NestJS #56. Authorization with roles and claims](https://wanago.io/2021/11/15/api-nestjs-authorization-roles-claims/)
- 57. [API with NestJS #57. Composing classes with the mixin pattern](https://wanago.io/2021/12/13/api-nestjs-mixin-pattern/)
- 58. [API with NestJS #58. Using ETag to implement cache and save bandwidth](https://wanago.io/2022/01/17/api-nestjs-etag-cache/)
- 59. [API with NestJS #59. Introduction to a monorepo with Lerna and Yarn workspaces](https://wanago.io/2022/01/31/api-nestjs-monorepo-lerna-yarn-workspaces/)
- 60. [API with NestJS #60. The OpenAPI specification and Swagger](https://wanago.io/2022/02/14/api-nestjs-openapi-swagger/)
- 61. [API with NestJS #61. Dealing with circular dependencies](https://wanago.io/2022/02/28/api-nestjs-circular-dependencies/)
- 62. [API with NestJS #62. Introduction to MikroORM with PostgreSQL](https://wanago.io/2022/05/23/api-nestjs-mikroorm-postgresql/)

There are quite a few things we can do when tackling our application’s performance. We sometimes can make our code faster and optimize the database queries. To make our API even more performant, we might want to completely avoid running some of the code.

Accessing the data stored in the database is quite often time-consuming. It adds up if we also perform some data manipulation on top of it before returning it to the user. Fortunately, we can improve our approach with **caching**. By storing a copy of the data in a way that it can be served faster, we can speed up the response in a significant way.

The most straightforward way to implement cache is to store the data in the memory of our application. Under the hood, NestJS uses the [cache-manager library.](https://www.npmjs.com/package/cache-manager) We need to start by installing it.

```bash
npm install cache-manager
```

To enable the cache, we need to import the `CacheModule` in our app.

posts.module.ts

```tsx
import { CacheModule, Module } from '@nestjs/common';
import PostsController from './posts.controller';
import PostsService from './posts.service';
import Post from './post.entity';
import { TypeOrmModule } from '@nestjs/typeorm';
import { SearchModule } from '../search/search.module';
import PostsSearchService from './postsSearch.service';
 
@Module({
  imports: [
    CacheModule.register(),
    TypeOrmModule.forFeature([Post]),
    SearchModule,
  ],
  controllers: [PostsController],
  providers: [PostsService, PostsSearchService],
})
export class PostsModule {}
```

By default, the amount of time that a response is cached before deleting it is 5 seconds. Also, the maximum number of elements in the cache is 100 by default. We can change those values by passing additional options to the `CacheModule.register()` method.

```tsx
CacheModule.register({
  ttl: 5,
  max: 100
});
```

### Automatically caching responses

NestJS comes equipped with the `CacheInterceptor`. With it, NestJS handles the cache automatically.

`posts.controller.ts`

```tsx
import {
  Controller,
  Get,
  UseInterceptors,
  ClassSerializerInterceptor,
  Query, CacheInterceptor,
} from '@nestjs/common';
import PostsService from './posts.service';
import { PaginationParams } from '../utils/types/paginationParams';
 
@Controller('posts')
@UseInterceptors(ClassSerializerInterceptor)
export default class PostsController {
  constructor(
    private readonly postsService: PostsService
  ) {}
 
  @UseInterceptors(CacheInterceptor)
  @Get()
  async getPosts(
    @Query('search') search: string,
    @Query() { offset, limit, startId }: PaginationParams
  ) {
    if (search) {
      return this.postsService.searchForPosts(search, offset, limit, startId);
    }
    return this.postsService.getAllPosts(offset, limit, startId);
  }
  // ...
}
```

If we call this endpoint two times, NestJS does not invoke the `getPosts` method twice. Instead, it returns the cached data the second time.

In the [twelfth part of this series](https://wanago.io/2020/09/07/api-nestjs-elasticsearch/), we’ve integrated Elasticsearch into our application. Also, in the [seventeenth part](https://wanago.io/2020/11/09/api-nestjs-offset-keyset-pagination-postgresql-typeorm/), we’ve added pagination. Therefore, our `/posts` endpoint accepts quite a few query params.

A very important thing that the [official documentation](https://docs.nestjs.com/techniques/caching) does not mention is that NestJS will store the response of the `getPosts` method separately for every combination of query params. Thanks to that, calling /posts?search=Hello and /posts?search=World can yield different responses.

Although above, we use `CacheInterceptor` for a particular endpoint, we can also use it for the whole controller. We could even use it for a whole module. Using cache might sometimes cause us to return stale data, though. Therefore, we need to be careful about what endpoint do we cache.

## Using the cache store manually

Aside from using the automatic cache, we can also interact with the cache manually. Let’s inject it into our service.

`posts.service.ts`

```tsx
import { CACHE_MANAGER, Inject, Injectable } from '@nestjs/common';
import Post from './post.entity';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import PostsSearchService from './postsSearch.service';
 
@Injectable()
export default class PostsService {
  constructor(
    @InjectRepository(Post)
    private postsRepository: Repository<Post>,
    private postsSearchService: PostsSearchService,
    @Inject(CACHE_MANAGER) private cacheManager: Cache
  ) {}
 
  // ...
 
}
```

An important concept to grasp is that the cache manager provides a key-value store. We can:

- retrieve the values using the `cacheManager.get('key')` method,
- add items using `cacheManager.set('key', 'value)`,
- remove elements with `cacheManager.del('key')`,
- clear the whole cache using `cacheManager.reset()`.

It can come in handy for more sophisticated cases. We can even use it together with the automatic cache.

### Invalidating cache

If we would like to increase the time in which our cache lives, we need to figure out a way to **invalidate it**. If we want to cache the list of our posts, we need to refresh it every time a post is added, modified, or removed.

To do use the `cacheManager.del` function to remove the cache, we need to know the key. The `CacheInterceptor` under the hood creates a key for every route we cache. This means that it creates separate cache keys both for `/posts` and `/posts?search=Hello`.

Instead of relying on `CacheInterceptor` to generate a key for every route, we can define it ourselves with the `@CacheKey` decorator. We can also use `@CacheTTL` to increase the time during which the cache lives.

`postsCacheKey.constant.ts`

```tsx
export const GET_POSTS_CACHE_KEY = 'GET_POSTS_CACHE';
```

`posts.controller.ts`

```tsx
@UseInterceptors(CacheInterceptor)
@CacheKey(GET_POSTS_CACHE_KEY)
@CacheTTL(120)
@Get()
async getPosts(
  @Query('search') search: string,
  @Query() { offset, limit, startId }: PaginationParams
) {
  if (search) {
    return this.postsService.searchForPosts(search, offset, limit, startId);
  }
  return this.postsService.getAllPosts(offset, limit, startId);
}
```

The above creates a big issue, though. Because now our custom key is always used for the `getPosts` method, it means that different query parameters yield the same result. Both `/posts` and `/posts?search=Hello` now use the same cache.

To fix this, we need to extend the `CacheInterceptor` class and change its behavior slightly. The `trackBy` method of the `CacheInterceptor` returns a key that is used within the store. Instead of returning the cache key, let’s add the query params to it.

> To view the original **`trackBy`** method, check out [this file in the repository.](https://github.com/nestjs/nest/blob/master/packages/common/cache/interceptors/cache.interceptor.ts#L65)
> 

`httpCache.interceptor.ts`

```tsx
import { CACHE_KEY_METADATA, CacheInterceptor, ExecutionContext, Injectable } from '@nestjs/common';
 
@Injectable()
export class HttpCacheInterceptor extends CacheInterceptor {
  trackBy(context: ExecutionContext): string | undefined {
    const cacheKey = this.reflector.get(
      CACHE_KEY_METADATA,
      context.getHandler(),
    );
 
    if (cacheKey) {
      const request = context.switchToHttp().getRequest();
      return `${cacheKey}-${request._parsedUrl.query}`;
    }
 
    return super.trackBy(context);
  }
}
```

If we don’t provide the `@CacheKey` decorator with a key, NestJS will use the original `trackBy` method through `super.trackBy(context)`.

Otherwise, the `HttpCacheInterceptor` will create keys like `POSTS_CACHE-null` and `POSTS_CACHE-search=Hello`.

Now we can create a `clearCache` method and use it when we create, update, and delete posts.

```tsx
import { CACHE_MANAGER, Inject, Injectable } from '@nestjs/common';
import CreatePostDto from './dto/createPost.dto';
import Post from './post.entity';
import UpdatePostDto from './dto/updatePost.dto';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import PostNotFoundException from './exceptions/postNotFound.exception';
import User from '../users/user.entity';
import PostsSearchService from './postsSearch.service';
import { Cache } from 'cache-manager';
import { GET_POSTS_CACHE_KEY } from './postsCacheKey.constant';
 
@Injectable()
export default class PostsService {
  constructor(
    @InjectRepository(Post)
    private postsRepository: Repository<Post>,
    private postsSearchService: PostsSearchService,
    @Inject(CACHE_MANAGER) private cacheManager: Cache
  ) {}
 
  async clearCache() {
    const keys: string[] = await this.cacheManager.store.keys();
    keys.forEach((key) => {
      if (key.startsWith(GET_POSTS_CACHE_KEY)) {
        this.cacheManager.del(key);
      }
    })
  }
 
  async createPost(post: CreatePostDto, user: User) {
    const newPost = await this.postsRepository.create({
      ...post,
      author: user
    });
    await this.postsRepository.save(newPost);
    this.postsSearchService.indexPost(newPost);
    await this.clearCache();
    return newPost;
  }
 
  async updatePost(id: number, post: UpdatePostDto) {
    await this.postsRepository.update(id, post);
    const updatedPost = await this.postsRepository.findOne(id, { relations: ['author'] });
    if (updatedPost) {
      await this.postsSearchService.update(updatedPost);
      await this.clearCache();
      return updatedPost;
    }
    throw new PostNotFoundException(id);
  }
 
  async deletePost(id: number) {
    const deleteResponse = await this.postsRepository.delete(id);
    if (!deleteResponse.affected) {
      throw new PostNotFoundException(id);
    }
    await this.postsSearchService.remove(id);
    await this.clearCache();
  }
 
  // ...
}
```

By doing the above, we invalidate our cache when the list of posts should change. With that, we can increase the Time To Live (TTL) and increase our application’s performance.

## Summary

In this article, we’ve implemented an in-memory cache both by using the auto-caching and interacting with the cache store manually. Thanks to adjusting the way NestJS tracks cache responses, we’ve also been able to appropriately invalidate our cache.

While the in-memory cache is valid in a lot of cases, it has its disadvantages. For example, it is not shared between multiple instances of our application. To deal with this issue, we can use Redis. We will cover this topic in upcoming articles, so stay tuned!