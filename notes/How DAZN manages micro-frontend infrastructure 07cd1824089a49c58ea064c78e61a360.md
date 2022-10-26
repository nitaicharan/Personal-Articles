# How DAZN manages micro-frontend infrastructure

Created: April 24, 2022 9:21 PM
Finished: No
Source: https://medium.com/dazn-tech/how-dazn-manages-micro-frontend-infrastructure-f045d7c634c2

Hi! I’m Simon, Head of Core Services. Before forming this team last year, I was a Principal Engineer, focused on engagement and our main frontend infrastructure. The Core Services team manage some critical infrastructure for our micro-frontend architecture. We maintain Bootstrap, our custom micro-front end loading library, and services like Geofencing which ensure the right content is served to the right people globally.

# Why do we have a Core Services team?

DAZN Engineering has grown to more than 500 people and 60 teams, distributed across many countries. Due to this growth, in 2020, we reviewed our company structure and moved some of our engineering teams around to better suit domain-driven development, or areas of focus, rather than locations. Our motto throughout was **“You build it, you own it”**–empowering our engineering teams to make decisions and take ownership of their domain. During this review, we discussed where Bootstrap, and the associated services + team, should live.

It became clear that the existing Bootstrap team needed to live in the Platform + Tooling domain. Bootstrap, and the team’s other services, provide critical components which are used by all the other domains, which is the perfect definition for Platform.

Whilst reviewing the ownership of other services, we found there were more that fit the definition of Platform. So, with a combination of the existing Bootstrap team and some fantastic engineers who were willing to take on the challenge, Core Services was formed.

We’re an engineering-led team, remote-first and distributed between Leeds, Manchester, Amsterdam, and London. We manage our processes ourselves, choosing how to run our team meetings and adapting to suit our needs as they evolve. There are currently 7 engineers, including myself, working primarily with JavaScript (and Typescript), AWS, and Terraform.

# What does a Core Services team do?

The services we maintain cover the primary user journey of entering [https://dazn.com](https://dazn.com/) into your browser, all the way to having a working frontend loaded on your page. Because of that, our services have high scalability requirements and must be resilient to failure. Here are a few of them…

## Bootstrap

This library powers our micro-frontends architecture. It’s a single `index.html` file that determines which chapter (effectively a section of the website) to load.

Once loaded, it injects the chapter content into the page and provides a set of common APIs for the chapter to interact with. For example, it allows chapters to read a user’s information, such as location and subscription status, and also exposes APIs to navigate to other pages.

**Tech stack:** TypeScript, built using Webpack and served via CloudFront + S3

[Read more about micro-frontends @ DAZN here](https://medium.com/dazn-tech/adopting-a-micro-frontends-architecture-e283e6a3c4f3)

## Startup

Every client (web, TV, mobile…) calls Startup as soon as the application is loaded. It serves shared configuration, such as a service dictionary, based on the browser, platform, location etc. It’s the first API call that any client will make, making it critical to the operation of DAZN.

**Tech stack:** Node.js with TypeScript, running on Elastic Container Service (ECS Fargate) with Application Load Balancers (ALBs), running in multiple AWS regions

## Geofencing

Many DAZN services need to know where a user is located, and Geofencing supports that. In fact, Geofencing is called by Startup, so we can check where a user is and serve them the correct content and experiences. Single-digit latency figures are essential for Geofencing, as any latency introduced here will delay users from seeing our content.

**Tech stack:** Node.js with TypeScript, running on ECS Fargate with ALBs running in multiple AWS regions behind CloudFront. Some tooling is written in Go.

## Pubby

Our WebSockets-based broadcasting system powers many real-time features on DAZN, such as Key Moments and DAZN Pulse. It’s broadcasting hundreds of millions of messages per day.

**Tech stack:** Node.js with JavaScript, DynamoDB, Redis, Lambda, SNS, ECS Fargate, ALBs

Read more:

![How%20DAZN%20manages%20micro-frontend%20infrastructure%2007cd1824089a49c58ea064c78e61a360/1s_1IjU_hwvCDLTyGQ5-5jA.png](How%20DAZN%20manages%20micro-frontend%20infrastructure%2007cd1824089a49c58ea064c78e61a360/1s_1IjU_hwvCDLTyGQ5-5jA.png)

—

The list is longer than this, but the services above should give a good snapshot of what we do!

# What challenges are you facing?

At the start of the year, we focused on taking ownership of our services. We needed to gain an in-depth understanding of our services as quickly as possible. To achieve this, we ran regular chaos tests (check out our open-source JavaScript chaos-testing library — [chaos-squirrel](https://github.com/getndazn/chaos-squirrel)) and fire-drills to observe how the services performed under pressure, and how we could expect them to fail.

After taking ownership, we looked to simplify and improve the readability code wherever possible, which included some refactoring from JavaScript into TypeScript. We made performance improvements whilst making sure our observability was top-notch so we can see the impact of our changes.

# What are the next features on your roadmap?

We’re using a mindmap in [Miro](https://miro.com/) to track our ideas (we have loads!), categorised under Simplification, Performance, Observability/Reliability, DX/Team Efficiency, and Security. We have a bi-weekly Miro refinement session, where each team member can bring the ideas that they feel passionate about, so we can discuss and prioritise. We’re loving our Miro ideas board so far, and we’ll share more details about it in the near future.

Before the end of this year, we will have made significant performance improvements across our services. We want strict contracts for all API calls with client libraries to help consumers “do the right thing”. We’re aiming to have made drastic improvements to our shared configurations, which will reduce our time to market and improve the safety of config changes.

# We’re growing!

To achieve our goals, we’re looking to grow the team significantly this year. We’re advertising for both [frontend](https://careers.dazn.com/jobs/job/602f2812-frontend-software-engineer-core-services/) and [backend](https://careers.dazn.com/jobs/job/4c1b690c-backend-software-engineer-core-services/) specialists. So, if you’re interested in working in a supportive team, with services that handle millions of users, get in touch!

If you have any questions about the team, our roles, DAZN, or anything else, feel free to [drop me a message on Twitter](https://twitter.com/simon_tabor)!