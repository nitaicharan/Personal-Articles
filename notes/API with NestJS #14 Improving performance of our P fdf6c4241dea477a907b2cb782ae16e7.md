# API with NestJS #14. Improving performance of our Postgres database with indexes

Course relation: Series%20API%20with%20NestJS%20b9df00d705df4156a80e837561dd0cfc.md
Created: June 2, 2022 10:39 PM
Finished: Yes
Finished 📅: June 2, 2022
Order: 14
Source: https://wanago.io/2020/10/19/nestjs-performance-postgres-database-indexes/
Tags: #nestjs

![API%20with%20NestJS%20#14%20Improving%20performance%20of%20our%20P%20fdf6c4241dea477a907b2cb782ae16e7/nest14.png](API%20with%20NestJS%20#14%20Improving%20performance%20of%20our%20P%20fdf6c4241dea477a907b2cb782ae16e7/nest14.png)

- 7. [API with NestJS #7. Creating relationships with Postgres and TypeORM](https://wanago.io/2020/06/22/api-nestjs-relationships-postgres-typeorm/)
- 11. [API with NestJS #11. Managing private files with Amazon S3](https://wanago.io/2020/08/10/api-nestjs-private-files-amazon-s3/)
- 12. [API with NestJS #12. Introduction to Elasticsearch](https://wanago.io/2020/09/07/api-nestjs-elasticsearch/)
- 13. [API with NestJS #13. Implementing refresh tokens using JWT](https://wanago.io/2020/09/21/api-nestjs-refresh-tokens-jwt/)
- 14. API with NestJS #14. Improving performance of our Postgres database with indexes
- 15. [API with NestJS #15. Defining transactions with PostgreSQL and TypeORM](https://wanago.io/2020/10/26/api-nestjs-transactions-postgresql-typeorm/)
- 16. [API with NestJS #16. Using the array data type with PostgreSQL and TypeORM](https://wanago.io/2020/11/02/api-nestjs-array-data-type-postgresql-typeorm/)
- 18. [API with NestJS #18. Exploring the idea of microservices](https://wanago.io/2020/11/16/api-nestjs-microservices/)
- 19. [API with NestJS #19. Using RabbitMQ to communicate with microservices](https://wanago.io/2020/11/23/api-nestjs-rabbitmq-microservices/)
- 20. [API with NestJS #20. Communicating with microservices using the gRPC framework](https://wanago.io/2020/11/30/api-nestjs-microservices-grpc-framework/)
- 23. [API with NestJS #23. Implementing in-memory cache to increase the performance](https://wanago.io/2021/01/04/api-nestjs-in-memory-cache-performance/)
- 24. [API with NestJS #24. Cache with Redis. Running the app in a Node.js cluster](https://wanago.io/2021/01/11/nestjs-cache-redis-node-js-cluster/)
- 25. [API with NestJS #25. Sending scheduled emails with cron and Nodemailer](https://wanago.io/2021/01/18/api-nestjs-cron-nodemailer/)
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
- 42. [API with NestJS #42. Authenticating users with Google](https://wanago.io/2021/07/26/api-nestjs-google-authentication/)
- 44. [API with NestJS #44. Implementing relationships with MongoDB](https://wanago.io/2021/08/23/api-nestjs-relationships-mongodb/)
- 45. [API with NestJS #45. Virtual properties with MongoDB and Mongoose](https://wanago.io/2021/08/30/api-nestjs-virtual-properties-mongodb-mongoose/)
- 46. [API with NestJS #46. Managing transactions with MongoDB and Mongoose](https://wanago.io/2021/09/06/api-nestjs-transactions-mongodb-mongoose/)
- 48. [API with NestJS #48. Definining indexes with MongoDB and Mongoose](https://wanago.io/2021/09/20/api-nestjs-indexes-mongodb-mongoose/)
- 50. [API with NestJS #50. Introduction to logging with the built-in logger and TypeORM](https://wanago.io/2021/10/04/api-nestjs-logging-typeorm/)
- 51. [API with NestJS #51. Health checks with Terminus and Datadog](https://wanago.io/2021/10/11/api-nestjs-health-checks-terminus-datadog/)
- 52. [API with NestJS #52. Generating documentation with Compodoc and JSDoc](https://wanago.io/2021/10/18/api-nestjs-documentation-compodoc-jsdoc/)
- 55. [API with NestJS #55. Uploading files to the server](https://wanago.io/2021/11/08/api-nestjs-uploading-files-to-server/)

As our system grows, certain queries on our database might fail us in terms of performance. One of the popular ways of dealing with this issue are **indexes**. This article explores how we can use them both through TypeORM and writing our own Postgres queries.

## Introduction to indexes

When we store information on a disk, we do so with blocks of data. When searching through it, we need to scan the entirety of it to find matching entries. Iterating over it from cover to cover does not seem like the most performant approach.

In the [second part of this series](https://wanago.io/2020/05/18/api-nestjs-postgresql-typeorm/), we’ve created a table of posts.

![API%20with%20NestJS%20#14%20Improving%20performance%20of%20our%20P%20fdf6c4241dea477a907b2cb782ae16e7/Screenshot-from-2020-10-18-15-12-38.png](API%20with%20NestJS%20#14%20Improving%20performance%20of%20our%20P%20fdf6c4241dea477a907b2cb782ae16e7/Screenshot-from-2020-10-18-15-12-38.png)

One of the most common queries that we might want to run here is to find posts of a

```tsx
SELECT * FROM post WHERE "authorId" = 1;
```

Unfortunately, this means scanning the entire `post` table to find matching entries. As our table grows, this is going to take more and more time. We can improve this with the help of **indexes**.

The job of indexes is to make our queries faster. It requires quite a bit of disk space by holding a copy of the indexed field values and pointing to the record they relate to.

```tsx
CREATE INDEX post_authorId_index ON post ("authorId");
```

> Postgres folds column names that we don’t put in double quotes to lower case. This is why we need to write "`authorId`" above
> 

We can imagine them as **key and value pairs**. In our case, the keys would be ids of the authors, and the values would be pointers to the posts. This way, Postgres has a lot easier time finding all of the posts of a certain author.

This information is stored in a separate data structure. Whenever we query the data, Postgres can use it under the hood to increase the speed.

Unfortunately, it takes a noticeable amount of space, and Postgres needs to keep it synchronized. Every time we insert or update the data, Postgres needs to update the indexes too. When thinking about adding indexes, we need to consider the pros and cons.

> Indexes could benefit our update queries if they have some search conditions, though.
> 

## Types of scans

Please note that the above **select** query that we perform needs to extract the data because we want to access the posts’ contents. Postgres has a concept of **index-only** scans when the index contains all information required by a query. For example, when we count the number of posts, we might experience an even greater improvement in speed because Postgres does not need to read our table’s contents.

The type of scan is chosen under the hood by Postgres. We can inspect it using the `EXPLAIN` command.

```tsx
EXPLAIN SELECT * FROM post WHERE authorId = 1;
```

We can expect one of a few different scans to be applied:

- sequential scan
    - sequentially scanning all items of a table
- index scan
    - uses indexes to increase the performance of the scan. Accesses the data from the index and uses it to fetch the data from the actual table
- index-only scan
    - also uses indexes but only scans the index data structure
- bitmap scan
    - a process between an index scan and sequential scan

For a more detailed comparison of various scan methods, check out [this article](https://severalnines.com/database-blog/overview-various-scan-methods-postgresql).

## Implementing Indexes with TypeORM

So far, in this series, we’ve been using TypeORM. We can use it to generate indexes for certain columns using the @Index() decorator.

```tsx
import { Entity, ManyToOne, PrimaryGeneratedColumn, Index } from 'typeorm';
import User from '../users/user.entity';
 
@Entity()
class Post {
  @PrimaryGeneratedColumn()
  public id: number;
 
  // ...
 
  @Index()
  @ManyToOne(() => User, (author: User) => author.posts)
  public author: User;
}
 
export default Post;
```

After firing up pgAdmin, we can see that TypeORM generated a name for our index.

![API%20with%20NestJS%20#14%20Improving%20performance%20of%20our%20P%20fdf6c4241dea477a907b2cb782ae16e7/Screenshot-from-2020-10-18-18-02-56.png](API%20with%20NestJS%20#14%20Improving%20performance%20of%20our%20P%20fdf6c4241dea477a907b2cb782ae16e7/Screenshot-from-2020-10-18-18-02-56.png)

We can avoid the above behavior by providing a name when using the @Index() decorator.

```tsx
import { Entity, ManyToOne, PrimaryGeneratedColumn, Index } from 'typeorm';
import User from '../users/user.entity';
 
@Entity()
class Post {
  @PrimaryGeneratedColumn()
  public id: number;
 
  // ...
 
  @Index('post_authorId_index')
  @ManyToOne(() => User, (author: User) => author.posts)
  public author: User;
  
}
 
export default Post;
```

## Multicolumn indexes

We might sometimes find ourselves making queries with multiple conditions, such as:

```tsx
SELECT * FROM post WHERE "authorId" = 1 AND "categoryId" = 2
```

The performance of the above might be improved by creating an index that uses two columns.

```tsx
CREATE INDEX post_authorId_columnId_index ON post ("authorId", "columnId");
```

TypeORM also supports indexes with multiple columns. To specify it, we need to use the `@Index()` decorator on the entity.

```tsx
@Entity()
@Index(['postId', 'authorId'])
class Post {
  // ...
}
```

Keep in mind that Postgres states in [its documentation](https://www.postgresql.org/docs/9.1/indexes-multicolumn.html) that *multicolumn indexes should be used sparingly*. Usually, an index on a single column is enough, and using more than three columns probably won’t be helpful.

## Index types

Postgres has a few index types available under the hood. By default, it uses B-tree indexes that fit most cases. We also have a few other options:

- Generalized Inverted Indexes (GIN)
    - designed to handle cases where the values contain more than one key – for example, arrays
- Hash indexes
    - can only handle simple equality checks
- Block Range Indexes (BRIN)
    - used for large tables with columns that have a linear sort order
- Generalized Search Try (GIST)
    - useful for indexing geometric data and text search

Unfortunately, TypeORM [does not support creating indexes with custom types](https://github.com/typeorm/typeorm/issues/1519). If we’d need one of the above types, we would have to write the query ourselves. For example, we could [write a migration](https://github.com/typeorm/typeorm/blob/master/docs/migrations.md) with it.

## Summary

In this article, we’ve looked into the basics of creating indexes in the Postgres database. We’ve also briefly touched on the subject of various index types. To better understand how our database works, we also used the EXPLAIN command to see how effective our indexes are. Since indexes can substantially improve our application’s performance if used currently, they are definitely worth checking out.