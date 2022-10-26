# API with NestJS #24. Cache with Redis. Running the app in a Node.js cluster

Course relation: Series%20API%20with%20NestJS%20b9df00d705df4156a80e837561dd0cfc.md
Created: May 27, 2022 1:52 AM
Finished: Yes
Finished 📅: May 27, 2022
Order: 24
Source: https://wanago.io/2021/01/11/nestjs-cache-redis-node-js-cluster/
Tags: #nestjs

![API%20with%20NestJS%20#24%20Cache%20with%20Redis%20Running%20the%20a%200d7c1a00c11543079102a66631680b77/nest24.png](API%20with%20NestJS%20#24%20Cache%20with%20Redis%20Running%20the%20a%200d7c1a00c11543079102a66631680b77/nest24.png)

- 11. [API with NestJS #11. Managing private files with Amazon S3](https://wanago.io/2020/08/10/api-nestjs-private-files-amazon-s3/)
- 12. [API with NestJS #12. Introduction to Elasticsearch](https://wanago.io/2020/09/07/api-nestjs-elasticsearch/)
- 14. [API with NestJS #14. Improving performance of our Postgres database with indexes](https://wanago.io/2020/10/19/nestjs-performance-postgres-database-indexes/)
- 15. [API with NestJS #15. Defining transactions with PostgreSQL and TypeORM](https://wanago.io/2020/10/26/api-nestjs-transactions-postgresql-typeorm/)
- 16. [API with NestJS #16. Using the array data type with PostgreSQL and TypeORM](https://wanago.io/2020/11/02/api-nestjs-array-data-type-postgresql-typeorm/)
- 18. [API with NestJS #18. Exploring the idea of microservices](https://wanago.io/2020/11/16/api-nestjs-microservices/)
- 19. [API with NestJS #19. Using RabbitMQ to communicate with microservices](https://wanago.io/2020/11/23/api-nestjs-rabbitmq-microservices/)
- 20. [API with NestJS #20. Communicating with microservices using the gRPC framework](https://wanago.io/2020/11/30/api-nestjs-microservices-grpc-framework/)
- 23. [API with NestJS #23. Implementing in-memory cache to increase the performance](https://wanago.io/2021/01/04/api-nestjs-in-memory-cache-performance/)
- 24. API with NestJS #24. Cache with Redis. Running the app in a Node.js cluster
- 27. [API with NestJS #27. Introduction to GraphQL. Queries, mutations, and authentication](https://wanago.io/2021/02/01/api-nestjs-graphql-introduction/)
- 29. [API with NestJS #29. Real-time updates with GraphQL subscriptions](https://wanago.io/2021/02/15/api-nestjs-real-time-graphql-subscriptions/)
- 31. [API with NestJS #31. Two-factor authentication](https://wanago.io/2021/03/08/api-nestjs-two-factor-authentication/)
- 32. [API with NestJS #32. Introduction to Prisma with PostgreSQL](https://wanago.io/2021/03/29/api-nestjs-prisma-postgresql/)
- 33. [API with NestJS #33. Managing PostgreSQL relationships with Prisma](https://wanago.io/2021/04/05/api-nestjs-33-postgresql-relationships-prisma/)
- 34. [API with NestJS #34. Handling CPU-intensive tasks with queues](https://wanago.io/2021/05/03/api-nestjs-cpu-intensive-tasks-queues/)
- 36. [API with NestJS #36. Introduction to Stripe with React](https://wanago.io/2021/06/14/api-nestjs-stripe-react/)
- 37. [API with NestJS #37. Using Stripe to save credit cards for future use](https://wanago.io/2021/06/21/api-nestjs-stripe-save-credit-cards/)
- 39. [API with NestJS #39. Reacting to Stripe events with webhooks](https://wanago.io/2021/07/05/api-nestjs-stripe-events-webhooks/)
- 40. [API with NestJS #40. Confirming the email address](https://wanago.io/2021/07/12/api-nestjs-confirming-email/)
- 41. [API with NestJS #41. Verifying phone numbers and sending SMS messages with Twilio](https://wanago.io/2021/07/19/api-nestjs-verifying-phone-numbers-sending-sms-twilio/)
- 42. [API with NestJS #42. Authenticating users with Google](https://wanago.io/2021/07/26/api-nestjs-google-authentication/)
- 43. [API with NestJS #43. Introduction to MongoDB](https://wanago.io/2021/08/16/api-nestjs-mongodb/)
- 44. [API with NestJS #44. Implementing relationships with MongoDB](https://wanago.io/2021/08/23/api-nestjs-relationships-mongodb/)
- 45. [API with NestJS #45. Virtual properties with MongoDB and Mongoose](https://wanago.io/2021/08/30/api-nestjs-virtual-properties-mongodb-mongoose/)
- 48. [API with NestJS #48. Definining indexes with MongoDB and Mongoose](https://wanago.io/2021/09/20/api-nestjs-indexes-mongodb-mongoose/)
- 51. [API with NestJS #51. Health checks with Terminus and Datadog](https://wanago.io/2021/10/11/api-nestjs-health-checks-terminus-datadog/)
- 55. [API with NestJS #55. Uploading files to the server](https://wanago.io/2021/11/08/api-nestjs-uploading-files-to-server/)

Redis is a fast and reliable key-value store. It keeps the data in its memory, although Redis, by default, writes the data to the file system at least every 2 seconds.

In the [previous part of this series](https://wanago.io/2021/01/04/api-nestjs-in-memory-cache-performance/), we’ve used a cache stored in our application’s memory. While it is simple and efficient, it has its downsides. With applications where performance and availability are crucial, we often run multiple instances of our API. With that, the incoming traffic is load-balanced and redirected to multiple instances.

Unfortunately, keeping the cache within the memory of the application means that multiple instances of our API do not share the same cache. Also, restarting the API means losing the cache. Because of all of that, it is worth looking into Redis.

## Setting up Redis

Within this series, we’ve used Docker Compose to set up our architecture. It is also very straightforward to set up Redis with Docker. By default, Redis works on port `6379`.

`docker-compose.yml`

```yaml
version: "3"
services:
  redis:
    image: "redis:alpine"
    ports:
      - "6379:6379"
# ...
```

To connect Redis to NestJS, we also need the `cache-manager-redis-store` library.

```bash
npm install cache-manager-redis-store
```

Unfortunately, this library is not prepared to work with TypeScript. To deal with that, we can create our own declaration file.

`cacheManagerRedisStore.d.ts`

```jsx
declare module 'cache-manager-redis-store' {
  import { CacheStoreFactory } from '@nestjs/common/cache/interfaces/cache-manager.interface';
 
  const cacheStore: CacheStoreFactory;
 
  export = cacheStore;
}
```

To connect to Redis, we need two new environment variables: the host and the port.

`app.module.ts`

```jsx
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
import * as Joi from '@hapi/joi';
 
@Module({
  imports: [
    ConfigModule.forRoot({
      validationSchema: Joi.object({
        REDIS_HOST: Joi.string().required(),
        REDIS_PORT: Joi.number().required(),
        // ...
      })
    }),
    // ...
  ],
  controllers: [],
  providers: [],
})
export class AppModule {}
```

`.env`

```bash
REDIS_HOST=localhost
REDIS_PORT=6379
# ...
```

Once we do all of the above, we can use it to establish a connection with Redis.

`posts.module.ts`

```tsx
import * as redisStore from 'cache-manager-redis-store';
import { CacheModule, Module } from '@nestjs/common';
import PostsController from './posts.controller';
import PostsService from './posts.service';
import Post from './post.entity';
import { TypeOrmModule } from '@nestjs/typeorm';
import { SearchModule } from '../search/search.module';
import PostsSearchService from './postsSearch.service';
import { ConfigModule, ConfigService } from '@nestjs/config';
 
@Module({
  imports: [
    CacheModule.registerAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
        useFactory: (configService: ConfigService) => ({
          store: redisStore,
          host: configService.get('REDIS_HOST'),
          port: configService.get('REDIS_PORT'),
          ttl: 120
        }),
    }),
    TypeOrmModule.forFeature([Post]),
    SearchModule,
  ],
  controllers: [PostsController],
  providers: [PostsService, PostsSearchService],
})
export class PostsModule {}
```

## Managing our Redis server with an interface

As we use our app, we might want to look into our Redis data storage. A straightforward way to do that would be to set up [Redis Commander](https://github.com/joeferner/redis-commander) through Docker Compose.

`docker-compose.yml`

```yaml
version: "3"
services:
  redis:
    image: "redis:alpine"
    ports:
      - "6379:6379"
 
  redis-commander:
    image: rediscommander/redis-commander:latest
    environment:
      - REDIS_HOSTS=local:redis:6379
    ports:
      - "8081:8081"
    depends_on:
        - redis
# ...
```

> With `depends_on` above we make sure that redis has been started before running Redis Commander
> 

Running Redis Commander in such a way creates a web user interface that we can see at http://localhost:8081/.

Thanks to the way we set up the cache in the previous part of this series, we can now have multiple cache keys for the `/posts` endpoint.

![API%20with%20NestJS%20#24%20Cache%20with%20Redis%20Running%20the%20a%200d7c1a00c11543079102a66631680b77/Screenshot-from-2021-01-10-19-54-25.png](API%20with%20NestJS%20#24%20Cache%20with%20Redis%20Running%20the%20a%200d7c1a00c11543079102a66631680b77/Screenshot-from-2021-01-10-19-54-25.png)

## Running multiple instances of NestJS

JavaScript is single-threaded in nature. Although that’s the case, in the [tenth article of the Node.js TypeScript series](https://wanago.io/2019/04/22/typescript-node-js-threads-child-processes/), we’ve learned that Node.js is capable of performing multiple tasks at a time. Aside from the fact that it runs input and output operations in separate threads, Node.js allows us to create multiple processes.

To prevent heavy traffic from putting a strain on our API, we can also launch a **cluster** of Node.js processes. Such child processes share server ports and work under the same address. With that, the cluster works as a **load balancer**.

> With Node.js we can also use Worker Threads. To read more about it, check out Node.js TypeScript #12. Introduction to Worker Threads with TypeScript
> 

`runInCluster.ts`

```tsx
import * as cluster from 'cluster';
import * as os from 'os';
 
export function runInCluster(
  bootstrap: () => Promise<void>
) {
  const numberOfCores = os.cpus().length;
 
  if (cluster.isMaster) {
    for (let i = 0; i < numberOfCores; ++i) {
      cluster.fork();
    }
  } else {
    bootstrap();
  }
}
```

In the example above, our main process creates a child process for each core in our CPU. By default, Node.js uses the *round-robin* approach in which the master process listens on the port we’ve opened. It accepts incoming connections and distributes them across all of the processes in our cluster. Round-robin is a default policy on all platforms except Windows.

> If you want to read more about the cluster and how to change the scheduling policy, check out Node.js TypeScript #11. Harnessing the power of many processes using a cluster
> 

To use the above logic, we need to supply it with our `bootstrap` function. A fitting place for that would be the `main.ts` file:

`main.ts`

```tsx
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import * as cookieParser from 'cookie-parser';
import { ValidationPipe } from '@nestjs/common';
import { ExcludeNullInterceptor } from './utils/excludeNull.interceptor';
import { ConfigService } from '@nestjs/config';
import { config } from 'aws-sdk';
import { runInCluster } from './utils/runInCluster';
 
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe({
    transform: true
  }));
  app.useGlobalInterceptors(new ExcludeNullInterceptor());
  app.use(cookieParser());
 
  const configService = app.get(ConfigService);
  config.update({
    accessKeyId: configService.get('AWS_ACCESS_KEY_ID'),
    secretAccessKey: configService.get('AWS_SECRET_ACCESS_KEY'),
    region: configService.get('AWS_REGION'),
  });
 
  await app.listen(3000);
}
runInCluster(bootstrap);
```

On Linux, we can easily check how many processes our cluster spawns with `ps -e | grep node`:

![API%20with%20NestJS%20#24%20Cache%20with%20Redis%20Running%20the%20a%200d7c1a00c11543079102a66631680b77/Screenshot-from-2021-01-10-21-00-47.png](API%20with%20NestJS%20#24%20Cache%20with%20Redis%20Running%20the%20a%200d7c1a00c11543079102a66631680b77/Screenshot-from-2021-01-10-21-00-47.png)

## Summary

In this article, we added to the topic of caching by using Redis. One of its advantages is that the Redis cache can be shared across multiple instances of our application. To experience it, we’ve used the Node.js cluster to spawn multiple processes containing our API. The Node.js delegates the incoming requests to various processes, balancing the load.