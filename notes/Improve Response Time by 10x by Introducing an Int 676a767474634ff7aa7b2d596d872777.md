# Improve Response Time by 10x by Introducing an Interceptor In Nest.js

Created: September 27, 2022 8:20 PM
Finished: No
Source: https://betterprogramming.pub/improve-response-time-10x-by-introducing-an-interceptor-in-nestjs-590695692360
Tags: #nestjs

[Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/0kpXC2ia-jAiLRMGE](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/0kpXC2ia-jAiLRMGE)

Photo by [Aron Visuals](https://unsplash.com/@aronvisuals?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Writing scalable and fast APIs can be a challenge, and today I want to introduce you to a technique I implement in [NestJS](https://nestjs.com/) (Node.js) applications that have to process a high volume of incoming requests.

This approach is used for a specific use case — when your API executes some non-business logic during a client’s request. And we’ll review ways to offload that work, and will improve overall response times and requests per minute your API can handle.

Example use cases:

- Logging
- Metrics gathering
- Background job queue
- Client will receive a response via WebSocket

I have a [demo application ready to run some benchmarks](https://github.com/dkhorev/nestjs-interceptor-benchmark-demo). It’s a simple API route with variable conditions.

My examples are built around the [Redis database](https://redis.com/) and [Bull queue](https://www.npmjs.com/package/bull) module. Although other applications of this technique are possible, depending on your service of choice: Redis Streams, SNS, SQS, RabbitMQ, Kafka, or any regular DB.

# The Problem

Node.js is a single-threaded runtime for backend JavaScript code.

You always hear that advice: “Don’t block the event loop”.

What does that mean?

Let’s review a simple example, a request comes in, it hits your controller, you do some work, and return a response.

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1ttt4SgBSMRgoCCqf9bB7zw.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1ttt4SgBSMRgoCCqf9bB7zw.png)

A basic API request-response workflow

Everything in this example is executed synchronously, so the event loop is blocked at this time. The more requests hit your API - the longer each new client has to wait for the response (assuming you have finite resources).

One thing stands out in this diagram: the client doesn’t care about any background work we do with our `Logs API`, so why should he wait for it to finish? If `Logs API` is a call to 3rd party service — then it adds another point of failure that can affect your clients.

## Solution 1 — Offload logs processing to a queue

We know Redis is fast, so queuing a job is fast too, right?

Let’s introduce a simple queue and send these logs in the background.

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1dz0C3CudQOqW5_EBYHIxNQ.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1dz0C3CudQOqW5_EBYHIxNQ.png)

A workflow where the client doesn’t have to wait for Logs API to finish

Redis is known for being a very fast in-memory store, inserting a job to Redis DB most times can be done in under 1ms.

Compared to a database `INSERT`, Redis’s writes are times faster. This change alone will bring you a lot of performance. But we know this operation is also blocking the event loop, it’s very fast, but still blocking.

An example of the such controller:

## Solution 2 — Offload logs processing to an Interceptor

`Interceptor` is what backend developers also call a `Middleware`.

**What is middleware?**

It’s a piece of code that sits between request and response. Our route handlers are middlewares in that sense too, but NestJS uses a slightly different naming — `Interceptors`.

You can execute custom code before requests hit your controller, and a great thing, middleware can execute after the response is sent.

The high-level idea is — the interceptor gets request info and does the blocking action only when the response is sent. That way incoming requests are not throttled by the Node.js process being busy, and the app can do async processing (i/o related) in the background.

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1rfNYtDWDBqeK-iHLtiE9oA.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1rfNYtDWDBqeK-iHLtiE9oA.png)

A workflow where the client doesn’t have to wait for Logs API and queue to finish

Of course, this adds an overhead of piping requests thru another code block, but in most cases, this improves your application scalability even further.

So let’s see some numbers.

# **Testing tools and scenarios**

[autocannon](https://github.com/mcollina/autocannon) — a nice tool to load test APIs

[Redis](https://redis.io/) — an in-memory database, primarily used for cache, queues, and pub/sub, but it has a lot of other cool modules and can act as a full-stack database for your apps.

I will run both solutions in 4 different scenarios:

- simulating a job add with a delay of `5ms` with `setTimeout`
- simulating a job add with a delay of `25ms` with `setTimeout`
- localhost Redis queue (zero network latency)
- remote Redis queue (some network latency)

And with 3 different concurrency levels:

- x10 requests per second
- x50 requests per second
- x100 requests per second

Legend for the benchmarks:

- `RPM` — requests per minute API handles (higher — better)
- `Latency` — time to response of the API route (lower — better)

## Test suite — 5ms delay on a job add

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/14gKlS_XlOphMZQp_xSDf2A.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/14gKlS_XlOphMZQp_xSDf2A.png)

Test suite results with 5ms synthetic delay. RPM — higher is better. Latency — lower is better.

It seems like there’s no clear winner here, but the route with Interceptor performs *8x better (Latency) and 5x better (RPM) on x10 concurrency*. Other concurrency tests — are where my single node process resource limit is hit, so results are pretty similar for both approaches (blocking process leading a bit on very high concurrency).

Imagine a distributed / load-balanced deploy (or clustered node app). Each process will act somewhere in the x10 result zone, so the Interceptor solution is a clear winner in my opinion.

# Test suite — 25ms delay on a job add

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1pFMvyG8suNSnskldjX8Vhg.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1pFMvyG8suNSnskldjX8Vhg.png)

Test suite results with 25ms synthetic delay. RPM — higher is better. Latency — lower is better.

As our 3rd party service response time grows, the blocking solution experiences more and more problems.

Notice RPM has dropped `x2-x3` vs `5ms` test run. Latency has also increased to `x2` at a minimum.

Numbers for the Interceptor solution look the same — great! No matter how long is our 3rd party service response — our API is still fast.

# **Getting more real**

Now let’s do something close to a real-world test. We know that DB inserts are slow, so I expect results to be of similar magnitude for such a test.

But I want to check Redis, which is usually very fast and can insert data in under 1ms.

My setup: [NestJS](https://nestjs.com/), [Bull](https://www.npmjs.com/package/bull) queue manager, async task inserts a job into Redis queue.

## Test suite — Redis queue, localhost

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1ryZlKoXmdLnACIK1PS9cAw.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1ryZlKoXmdLnACIK1PS9cAw.png)

Test suite results with Redis localhost. RPM — higher is better. Latency — lower is better.

Surprisingly, with Redis localhost it seems, there is not much difference, blocking the job queue in the controllers is even faster.

Now keep in mind, that we’re testing with a network latency of 0, so that can play a role in such scenarios.

## Test suite — Redis remote, **EU to EU**

In real prod deploy you will probably use a hosted version of Redis and it won’t be on the same host as your app, so network latency will matter. The best case scenario is the same region/VPC.

I have used an $8 cluster from [Redislabs](https://redis.com/).

![Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1taaGfO4XiigEMS70YXtpvg.png](Improve%20Response%20Time%20by%2010x%20by%20Introducing%20an%20Int%20676a767474634ff7aa7b2d596d872777/1taaGfO4XiigEMS70YXtpvg.png)

Test suite results with Redis remote. RPM — higher is better. Latency — lower is better.

Again, those results confirm my initial synthetic delay run. Non-blocking workflows, even for very high-speed inserts, are way better than blocking.

Now network latency became the most limiting factor, notice how the blocking version’s *Latency time* increased almost to *100ms* in all test cases.

The most spectacular point here happens with low x10 concurrency. Route with interceptor *responds almost x50 faster and can process x40 more requests*.

You can notice that RPM for the Interceptor version of the controller is similar in all test suites — that’s because it just hits my Node.js (1 CPU) resource limit. So there’s so much more potential to improve when using a clustered version of the app.

# Conclusion

We’ve seen improvements in *Latency in a range from 8x to 50x*. For *RPM it was in a range from 6x to 40x*.

Being conservative, it’s safe to say you’ll get at least a 10x performance increase from using Interceptor (middleware) for your non-mission critical workloads such as logging, metrics, and background jobs.

If you’re working on a middle or big project it’s nice to review some of your controllers for those things and save $$$ on processing resources.

For a small project with low RPM, I’d not bother, but keep this solution in mind for the future.