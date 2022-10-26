# CQRS Software Architecture Pattern: The Good, the Bad, and the Ugly

Created: May 18, 2022 7:54 PM
Finished: No
Source: https://betterprogramming.pub/cqrs-software-architecture-pattern-the-good-the-bad-and-the-ugly-e9d6e7a34daf
Subjects: best practices, design pattern
Tags: #design-pattern

[CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/0zX2jzLKgp-UHbnQA](CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/0zX2jzLKgp-UHbnQA)

Photo by [Jukan Tateisi](https://unsplash.com/@tateisimikito?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

The Command and Query Responsibility Segregation (CQRS) it’s an architectural pattern where the main focus is to separate the way of reading and writing data. This pattern uses two separate models:

- **Queries** — Which are responsible for reading data
- **Commands** — Which are responsible for update data

In a Nutshell -

> The Command and Query Responsibility Segregation (CQRS) pattern separates read and update operations for a data store.
> 
> 
> Implementing CQRS in your application can **maximize its performance, scalability, and security.**
> 

The image below illustrates a basic implementation of the CQRS Pattern. *(figure 1.)*

Later on I will address the sync between both storages.

![CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1RiWr-7_SdVIOSFzp9HneOQ.png](CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1RiWr-7_SdVIOSFzp9HneOQ.png)

figure 1. High level CQRS implementation

# Terminology

## Commands

Commands represent the intention of changing the state of an entity. They execute operations like Insert, Update, Delete. Commands objects alter state and do not return data.

Commands represent a business operation and are always in the imperative tense, because they are always telling the application server to do something.

## Queries

Queries are used to get data from the database. Queries objects only return data and do not make any changes.

Queries will only contain the methods for getting data. They are used to read the data in the database to return the DTOs to the client, which will be displayed in the user interface.

## Event sourcing

Event Sourcing ensures that all changes to application state are stored as a sequence of events.

Not just can we query these events, we can also use the event log to reconstruct past states, and as a foundation to automatically adjust the state to cope with retroactive changes.

## Change Data Capture

Change Data Capture (CDC) is a solution that captures change events from a database transaction log (or equivalent mechanism) and forwards those events to downstream consumers.

CDC ultimately allows application state to be externalized and synchronized with external stores of data.

CDC can be implemented using [Debezium](https://debezium.io/documentation/reference/stable/architecture.html) as:

- Embedded Engine — (Only for Java applications)
- Standalone Debezium server using Kafka connect.

# CQRS Pattern

## **When to use CQRS**

CQRS can be considered to be used on scenarios where:

- Has a high demand for data consumption
- Performance of data reads must be tuned separately from the performance of data writes, especially when the number of reads is much greater than the number of writes.
- There is a need for a team to focus on the complex domain model that is part of the write model, while another team can focus on the read model and user interface.
- The system is expected to evolve over time and might contain multiple versions of the model, or where business rules change regularly.
- Integration with other systems, especially in combination with event sourcing, where the temporal failure of one subsystem shouldn’t affect the availability of the others.
- You can afford stale data.

## Advantages of CQRS

- **Separation of concerns** — When the read and write sides are separate, the models become more maintainable and flexible. The read model is typically simpler, while the write model involves more complex business logic.
- **Independent scaling** —With CQRS, read and write workloads can scale independently, resulting in fewer lock contentions.
- **Optimized data schemas** — On the read side, a schema can be optimized for queries, and on the write side, a schema can be optimized for updates.
- **Security** — It’s easier to ensure that only the right domain entities are performing writes on the data.
- **Simpler queries** — Application queries can avoid complex joins by storing a materialized view in the read database.

## Implementation issues and considerations

- **Complexity** of the code and lead to a more complex application design especially when using messaging.
- **Messaging** — Although CQRS does not require messaging, it’s common to use messaging to process commands and publish update events. In that case, the application must handle message failures or duplicate messages.
- **Eventual consistency —** If you separate the read and write databases, the read data may be stale.

## Separated Databases

When working with CQRS, it’s also possible — but it’s not mandatory — to have separate databases:

- a “read database” only for reading the data and creating materialized views
- and a “write database” only for making changes.

When we deal with more complex applications like microservices or any kind of application that has a high demand for data consumption, the common approach can become unwieldy, because having much writing and reading in the same database can affect the performance of the application (read and write workloads have very different performance and scale requirements).

If separate read and write databases are used, they must be kept in sync. Typically this is accomplished by having the write model publish an event whenever it updates the database.

More information later about events and different approaches on keeping the read and write databases synchronized.

## Separated Services / Models

By separate models we most commonly mean different object models, probably running in different logical processes, perhaps on separate hardware.

It’s common to see CQRS system split into separate services communicating with [Event Collaboration](https://martinfowler.com/eaaDev/EventCollaboration.html). This allows these services to easily take advantage of [Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html).

## CAP theorem

The CAP Theorem states that between consistency, availability, and partition tolerance (which can generally be considered “scalability”) we can only have two.

> A huge advantage of CQRS is that you will be able to relax the consistency requirements of the read model in order to gain high availability and scalability.
> 

**So…Why we need the Transactional Outbox Pattern?**

Transactional Outbox pattern is used for reliable messaging and guaranteed delivery of events.

# Transactional Outbox Pattern

## Problem

Microservices often publish events after performing a database transaction.

Writing to the database and publishing an event are two different transactions and they have to be atomic.

A failure to publish an event can mean critical failure to the business process.

This approach works well until an error occurs between saving the entity object and publishing the corresponding event. Sending an event might fail at this point for many reasons:

- Network errors
- Message service outage
- Host failure

Whatever the error is, the result is that the `EntityCreated` event can't be published to the message bus. *(figure 2.)*

Other services won’t be notified that an entity has been created.

The `Entity` service now has to take care of various things that don't relate to the actual business process.

It needs to keep track of events that still need to be put on the message bus as soon as it’s back online.

![CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1wWr-r_4yu_hubGCgHrGtaA.png](CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1wWr-r_4yu_hubGCgHrGtaA.png)

figure 2. Error sending event to the message bus

## Solution

There’s a well-known pattern called “Transactional Outbox” that can help avoid these situations.

This pattern provides an effective solution to publish events reliably. The idea of this approach is to have an “Outbox” table in the command service’s database.

When receiving a request for creating entity (Command), not only an insert into the Entity table is done, but a record representing the event is also inserted into the Outbox table.

The two database actions are done as part of the same transaction. *(figure 3.)*

An asynchronous background process monitors the Outbox table for new entries and if there are any, it publishes the events to the Event Bus.

Transactional Outbox pattern guarantees messaging reliability and at-least-once delivery.

![CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1gdpGCuH9hTolZyAMghOZgQ.png](CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1gdpGCuH9hTolZyAMghOZgQ.png)

figure 3. Transactional Outbox Pattern

## The Outbox table

Outbox table records describe an event that happened in the command service.

For example, a JSON structure representing the creation or update of an entity, data on the entity itself, as well as contextual information, such as a use case identifier.

By explicitly emitting events via records in the outbox table, external consumers can be assured that events are structured correctly.

This also ensures that event consumers will not break when, for instance, the internal domain model or Entities table are changed.

# An Implementation Based on Change Data Capture

[Log-based Change Data Capture](https://debezium.io/blog/2018/07/19/advantages-of-log-based-change-data-capture/) (CDC) is a great fit for capturing new entries in the outbox table and stream them to Apache Kafka. As opposed to any polling-based approach, event capture happens with a very low overhead in near-realtime. Debezium comes with [CDC connectors](https://debezium.io/docs/connectors/) for several databases such as MySQL, Postgres and SQL Server.

The below diagram *(figure 4.)* describes the overall architecture which is centered about two microservices, the admin-service and user-service. since Debezium is used, both microservices are written should be written in Java.

Debezium tails the *transaction log* (“write-ahead log”, *WAL*) of the Admin service’s database in order to capture any new events in the outbox table and propagates them to Apache Kafka. refer to [Outbox Event Router](https://debezium.io/documentation/reference/stable/transformations/outbox-event-router.html) configuration for more about event routing.

![CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1Q_8aZcAntFRPgBXzl72FTA.png](CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1Q_8aZcAntFRPgBXzl72FTA.png)

Figure 4. Architecture of CQRS implementation with CDC

`id` — Unique id of each message; can be used by consumers to detect any duplicate events, e.g. when restarting to read messages after a failure. Generated when creating a new event.

`entity_type` — The type of the *aggregate root* to which a given event is related; the idea being, leaning on the same concept of domain-driven design.

`entity_id` — The id of the aggregate root that is affected by a given event; this could for instance be the id of a Catalog.

`topic` — the topic of event, e.g. "Catalog Created" or "Catalog deleted". Allows consumers to trigger suitable event handlers.

`payload`— a JSON structure with the actual event contents, e.g. containing a catalog information.

When registering the Debezium connector — we should make sure to discard Deletion of Events from Kafka Topics. By setting the `tombstones.on.delete` to `false`, no deletion markers ("tombstones") will be emitted by the connector when an event record gets deleted from the outbox table.

# An Implementation Based on Event Bus

Outbox message processing can be done in a separate process depends on the use-cases. However, instead of creating a new process, you also might use the same process but with in a separate thread, depending on the requirements. *(figure 5.)*

![CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1Auw2d-bJWHARgDpFbAZO0A.png](CQRS%20Software%20Architecture%20Pattern%20The%20Good,%20the%20B%2003395ee28a6f4d8185e888beaa6a25d2/1Auw2d-bJWHARgDpFbAZO0A.png)

figure 5.CQRS Architecture based on Event Bus.

The idea is to use a scheduler that will periodically run and process messages from the O*utbox table*. the task execution schedule can be configured to run every few seconds. The scheduler’s configuration really depends on how many messages you will have and need to be processed in your system.

As we’re using Postgres for persistency of the Outbox table, we will use Postgres’ CTE (common table expressions) to fetch 100 messages ordered by their time creation as well we identify and fetch messages which are being processed for more than 2 minutes. notice that we also set the status of the outbox message to PROCESSING in the same transaction.

Putting it all together —

As I wrote earlier, if there was an error between processing the message and publishing it to setting it as processed in the outbox table, the task would pick up that message after 2 minutes from last time it was picked in the next task iterations. that way we guarantee at-least-once-delivery.

## Outbox Message

`status` —

- PENDING — The outbox message is pending to be processed.
- PROCESSING — The outbox message is currently being processed. The purpose of introducing this status is to avoid multiple job scheduling execution running in different threads or application instances to pick up and process the same message. that way we minimize multiple message deliveries.
- PROCESSED — The outbox message was processed successfully and is ready to be deleted.

`picked_at` — The time in which the message was picked up by the job for processing. this value is also used to identify messages that were picked for processing but their status wasn’t set to PROCESSED. Messages that were picked for more than 2 minutes their status is set to PROCESSING will be picked again by the job.

The rest are self-explaining.

# The Bad and Ugly side of CQRS

## Complexity

Why is it so hard to add a simple entry to some list and return the new list as a response? Well it all depends on the context.

Let’s start with the more common context, where the data is on a single machine and the users are in a reasonable size, for example, hundreds or thousands of users. In our case CQRS was complex, We have prioritized availability where users favored to receive consistency.

Take, for example, Facebook or Twitter, which must serve millions of users at the same time and must optimize for scalability and availability. It doesn’t matter if you get 10 instead of 11 likes or comments for a couple of minutes as long as you can still use their platform and continue to be engaged. or for google to display the most updated search result instantly.

In this case, CQRS is not difficult since the effort of synchronizing several storages is less that the overhead of building single model that accomplishes everything. The increased complexity of synchronizing the two models is countered by reducing the scalability challenges.

## Application changes

CQRS adds complexity to the system, making it more difficult to change it. This only makes sense if you have some extreme performance issues that are better dealt with two data models rather than one. This does not apply to the majority of applications.

## Have some numbers first

It’s possible that CQRS is a good fit for you. Have you tried a simpler approaches first? Do you have any numbers to back up your claim that a typical relational database won’t suffice? Did you create a proof of concept (POC) to demonstrate the feasibility of the solution?

Last but not least, when it comes to eventual consistency it is the product owners to decide whether it fits the business requirements or not.

## Complex queries

IF you’re using a NoSQL, have you considered migrating to a RDBS? or have you considered using materialized views to pre-compute your queries? or even maybe data remodeling?

# Conclusion

CQRS and Event Sourcing aren’t a mystical combination. It’s important to grasp the many implications of the two patterns before starting on your journey. Otherwise, it is quite easy to make a total mess both on a technical and functional level

CQRS and/or Event Sourcing and/or CDC can be an excellent solution to many problems assuming you have a clear understanding of the constraints and downsides.

Do not rush with making decisions out of necessity that will go against your original plans for your business. Before implementing the CQRS pattern into your system make sure to do a POC, and other testing. creating a proof of concept to test your solution will ensure you arrive at the best version of it and will save you time and money in the process. Don’t be afraid to get your hands dirty.

# Further Reading

Thanks for reading!