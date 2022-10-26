# Microservices Architecture at NetFlix

Created: April 24, 2022 9:19 PM
Finished: No
Source: https://josuamarcelc.com/microservices-architecture-at-netflix/
Subjects: design pattern
Tags: #microservice

Check out the Microservices Architecture at Netflix!

Microservices Architecture

![Microservices%20Architecture%20at%20NetFlix%20f074cce13af24d1f837e490b7992f4d3/microservices-architecture-at-netflix.jpg](Microservices%20Architecture%20at%20NetFlix%20f074cce13af24d1f837e490b7992f4d3/microservices-architecture-at-netflix.jpg)

1. Client sends a Play request to Backend running on AWS. The request is handled by AWS Load balancer (ELB)

2. AWS ELB will forward that request to API Gateway Service running on AWS EC2 instances. That component, named Zuul, is built by the Netflix team to allow dynamic routing, traffic monitoring & security, etc

3. Application API component is the core business logic, in this scenario, the forwarded request from API Gateway Service is handled by Play API.

4. Play API will call a microservice or a sequence of microservices to fulfill the request.

5. Microservices are mostly stateless small programs, to control its cascading failure & enable resilience, each microservice is isolated from the caller processes by Hystrix.

6. Microservices can save to or get data from a data store during its process.

7. Microservices can send events for tracking user activities or other data to the Stream Processing Pipeline for either real-time processing of personalized recommendation.

8. The data coming out of the Stream Processing Pipeline can be persistent to other data stores such as AWS S3, Hadoop HDFS, Cassandra, etc.

credit to: [https://www.linkedin.com/in/stevenouri/](https://www.linkedin.com/in/stevenouri/)

Post Views: 1,176