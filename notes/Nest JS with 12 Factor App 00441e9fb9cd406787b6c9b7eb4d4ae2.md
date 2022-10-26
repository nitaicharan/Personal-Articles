# Nest JS with 12 Factor App

Created: February 24, 2022 9:26 PM
Finished: No
Source: https://tkssharma.medium.com/nest-js-with-12-factor-app-8fc2e14f9470

Guys Recently I have created a playlist on youTube explaining things about Nest JS Microservices and how to add 12 factor principles in this microservice

Let's Talk about this a bit More

The **Twelve-Factor App methodology** is a methodology for building [software-as-a-service](https://en.wikipedia.org/wiki/Software_as_a_service) applications. These best practices are designed to enable applications to be built with portability and resilience when deployed to [the web](https://en.wikipedia.org/wiki/The_web)

[https://en.wikipedia.org/wiki/Twelve-Factor_App_methodology](https://en.wikipedia.org/wiki/Twelve-Factor_App_methodology)

I have created this playlist to cover all these aspects by showing how all these can be applied to a simple nestjs service.

We started with basic nestjs app and setting up all different aspect of a clean microservice

- like linting
- test setup using jest for e2e and unit tests
- CI/CD setup for app deployment
- build and Migration script
- Modular structure for application modules
- health checks and application monitoring with security modules

[Nest%20JS%20with%2012%20Factor%20App%2000441e9fb9cd406787b6c9b7eb4d4ae2/0PtXoY4vFdNPpICXj](Nest%20JS%20with%2012%20Factor%20App%2000441e9fb9cd406787b6c9b7eb4d4ae2/0PtXoY4vFdNPpICXj)

Baseline template [https://github.com/tkssharma/12-factor-app-microservices/tree/master/nestjs-typeorm-postgres-baseline%20%2301](https://github.com/tkssharma/12-factor-app-microservices/tree/master/nestjs-typeorm-postgres-baseline%20%2301)

The twelve-factor methodology is not specific to Node.js and much of these tips are already general enough for any cloud.gov application.

- Pin Down NPM Package Versions with Yarn.lock
- Use Git Flow as a Reliable Version Control Model
- Manage Configuration Values with Environment Variables
- Build, Release and Run Containers with Docker Compose
- Run Stateless Docker Containers
- Export Services with Docker Port Binding
- Scale Docker Horizontally with Nginx Load Balancing
- Ensure Containers Run with High-Availability
- Run Consistent Dev, Stage & Prod Docker Environments
- Pipe Log Output to STDOUT with Docker

Please let me know if this helped in building microservices using all these 12 factors.