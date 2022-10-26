# Introducing the Compute@Edge Object Store — global, persistent storage for compute functions

Created: June 2, 2022 9:11 PM
Finished: No
Source: https://www.fastly.com/blog/introducing-the-compute-edge-object-store-global-persistent-storage-for-compute-functions#:~:text=Our%20new%20Object%20Store%20offers,and%20unlock%20new%20use%20cases.

Companies are seeing major gains with Compute@Edge. For example, by using our [serverless edge compute platform](https://www.fastly.com/products/edge-compute/serverless), [Edgemesh](https://www.fastly.com/customers/edgemesh) and [Loveholidays](https://www.fastly.com/customers/loveholidays) both drastically reduced latency while improving conversion rates and reducing costs. But with data still needing to be fetched from the central cloud, we heard from some developers that their use case exploration was still limited.

We heard that feedback and are excited to announce a new option for state at the edge. Our new Object Store offers global, durable storage for compute functions at the edge. With fast reads and writes from both the edge or via API, you can store, control, or cache your data to reduce origin dependency and unlock new use cases.

### Performance and scale

We built the Object Store with durability in mind so you can rest easy knowing your data is secure — but durability doesn’t need to come at the expense of performance. In fact, high levels of performance and scale were an essential requirement as we set our original scope, and we feel like we really achieved that.

The Object Store takes advantage of our [powerful, global network](https://www.fastly.com/products/cdn) with the scalability you can expect from Compute@Edge. Plus, our rapid purging means you can cache more content, knowing it takes seconds — rather than minutes or hours — to purge stale content.

### The ins and outs

One of our sales engineers was recently working on a new waiting room project that required state storage across the network and thought this was the perfect opportunity to test and demo the speed of the Object Store.

First, he wrote an Object Store access app with an endpoint to write an object to the Object Store and an endpoint to GET an object. The GET endpoint repeatedly checks for the updated value in the Object Store for up to 10 seconds. If it doesn't see the updated value within 10 seconds it gives up.

The second Compute@Edge app drives the test. It first writes a new value to the Object Store. Then, using an internal test mechanism, it asynchronously sends the GET request to a node on each of the POPs. When the app gets a response from a POP, it records the total transaction time, and the POP reports how long it took to get the updated value.

You can [see these results on a map for a visual indication of performance](https://object-store-tester.edgecompute.app/): all responses received turn green on the map, and as you hover over each POP, you see both times displayed in seconds. In practice, none of the updates has taken longer than 2 seconds. In other words, we’ve completely offloaded the customer’s responsibility for data replication management, and we’ve done so in a very performant way so there’s no more sacrificing performance for data coverage.

![Introducing%20the%20Compute@Edge%20Object%20Store%20%E2%80%94%20global%208c4706f507d2410e922af15d95065f6c/fastly_object-store-blog.gif](Introducing%20the%20Compute@Edge%20Object%20Store%20%E2%80%94%20global%208c4706f507d2410e922af15d95065f6c/fastly_object-store-blog.gif)

This demo does come with a few caveats. First, because of the nature of the internal mechanism used to run the GETs on specific POPs, some of them will be unreachable for various reasons. In this case, we ignore the POP, and it won’t turn green. Second, this test uses a very small (16 byte) object. In the future this will become variable.

### Rapid decision making on a global scale

We [recently broke down](https://www.fastly.com/blog/three-questions-that-make-edge-state-easier-to-design) a guide to approaching your state architecture and when you may want to consider each type of storage at the edge. Generally, the Object Store is the best fit for use cases that require rapid decision making on a global scale, such as:

- Managing redirects at the edge for a fast, seamless customer experience from anywhere around the world.
- Applying WAF rules close to your end users for safer and more secure user experiences.
- Storing personalized assets at the edge for a more customized user experience — without sacrificing performance.

### Give it a go

With the Object Store, the possibilities are endless to build software with global state while maintaining extremely minimal latency. If you’re already using Compute@Edge, contact your account team to be added to the Object Store beta. If you’re not yet using Compute@Edge, [sign up for a free trial](https://www.fastly.com/signup/edge-compute) and see what you’ve been missing.