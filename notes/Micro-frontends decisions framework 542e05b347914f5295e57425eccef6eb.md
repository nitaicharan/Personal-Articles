# Micro-frontends decisions framework

Created: September 9, 2022 5:14 PM
Finished: No
Source: https://lucamezzalira.com/2019/12/22/micro-frontends-decisions-framework/

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/rev-21-dec-01-nofooter.jpg](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/rev-21-dec-01-nofooter.jpg)

In the past year (2019) I spent a lot of time talking, writing, podcasting and just chatting with people about micro-frontends.
 I’m truly passionate about the topic, mainly because it’s still an unexplored land where challenges arise every single day.

During all my talks, workshops and webinars I was building step by step a mental model that allowed me to simplify my thoughts for making them approachable by people listening to me or reading my contents.
 I’m sure there is still a lot of work to do but from the feedback I received so far, I was able to explain in a reasonable way what I had in my mind.

Recently I had the pleasure to do a 2h workshop in Bergen (Norway) about this topic and during my presentation (literally on the stage) I realized that I was very close to simplify even further the decisions process for embracing a micro-frontends architecture.
 In *JavaScriptland* we are used to choosing a framework and getting along with that for the entire lifecycle of a project, sometimes we change it in favor of another one but many times we stick with it for many months/years till the end of life for a specific project.
 Every time we use a JavaScript framework, someone else made architectural decisions that we live with (or trying to) and we focus on what matters the most from a business point of view: the features of an application.
 This doesn’t mean we have an easy challenge in front of us at all, we still have an essential part of this “game” taking the right design decisions for delivering the project, but first, we need to focus on having a solid base that allows us to build on top of it, aka the architecture.

Martin Fowler defines [software architecture](https://youtu.be/DngAZyWMGR0) as:

> 
> 
> 
> “Software architecture is those decisions which are both important and hard to change”
> 

Based on this definition, focusing on the key things, even better, the most important decisions for defining a micro-frontends architecture I came up with 4 pillars we have to decide upfront when we start architecting a micro-frontends project.
 Those pillars can be summarized in:

Each of them plays a fundamental role in the good outcome of a micro-frontends project.
 After taking those 4 decisions there will be many others to take along the journey but with those cornerstones, we should be able to cascade vast majority of the other decisions without any need of evaluating again our architecture.
 Bear in mind that those decisions could change over the lifecycle of a project but changing them will require a considerable development effort in order to achieve a coherent project’s architecture.

# Defining Micro-Frontends

The first decision to take is how we identify a micro-frontend, is it a part of a view (horizontal split) or the entire view (vertical split)?
 Splitting horizontally would mean identifying micro-frontends inside the same view, therefore multiple teams are taking care of the page composition coordinating themselves for the final result presented to a user.

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/ddf54-17u-4lqq45d9yfescbdec8a.png](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/ddf54-17u-4lqq45d9yfescbdec8a.png)

Splitting vertically, instead, would assign a specific view, or group of views, to a team allowing that team mastering a specific area of the application.

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/953da-1km_wghw9jytm0wd2rmq-tq.png](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/953da-1km_wghw9jytm0wd2rmq-tq.png)

In general, deciding to approach a micro-frontends project with a vertical slicing will simplify many decisions to take further down the line, because it’s closer the way a frontend developer is used to work so many practices can be easily applied to this approach.
 Also, the coordination between teams should be easier considering each team is owning a vertical slice of the application and not multiple parts spread across the application.

There are additional challenges when we decide to go with the horizontal split, anyway I described more in-depth how to [identify micro-frontends in another post](https://medium.com/dazn-tech/identifying-micro-frontends-in-our-applications-4b4995f39257) available on this website.

# Micro-Frontends Composition

The second decision is understanding where we want to compose micro-frontends, here we have 3 options to choose from:

- client-side composition
- edge-side composition
- server-side composition

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/b5b58-1veyy4t9_ntzcqedni0xega.png](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/b5b58-1veyy4t9_ntzcqedni0xega.png)

Client-side means implementing techniques like an App Shell loading single page applications, for instance, check out [Single-SPA](https://single-spa.js.org/) project, with iframes like [Luigi framework](https://luigi-project.io/) or via a library using [client-side transclusion](https://gustafnk.github.io/microservice-websites/#client-side-transclusion-with-h-include) technique.
 Implementing over the edge would mean using CDNs capabilities like [Edge-side includes](https://en.wikipedia.org/wiki/Edge_Side_Includes) (not always available in all the CDN providers or not fully implemented) or via computation happening on/near the edge (bear in mind there are restrictions in how much logic we could run with a solution like lambda@edge for instance).
 Finally, on the server-side, there are plenty of frameworks like [Ara Framework](https://ara-framework.github.io/website/), [Open Components](https://opencomponents.github.io/), [Piral](https://piral.io/) or [Tailor.js](https://github.com/zalando/tailor) and many others.
 When we decide to go with a vertical split we should aim for a client-side composition using Single-SPA or similar mechanisms like we do in DAZN.
 Instead, when we decide to identify our micro-frontends as a single part of a view we have all the options available (client-side, edge-side and server-side).

# Micro-Frontends Routing

After defining our micro-frontends composition strategy we need to decide how we want to route our views.
 This time the decision is not mutually exclusive, we can decide to route client-side and adding logic on the edge or having the routing logic on the client or server only.
 In this case, my suggestion is to be coherent with the composition part if you have decided to go with a client-side composition, use the app shell for mapping the routing logic, if you have chosen the server-side composition, use the webserver for managing the routing logic.
 In the case we decided to go with the edge-side composition we will rely on the URL associated with a page.

In the following diagram, you can easily discover the different routing possibilities associated with a composition technique.

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/5da22-1btneiw9tcxu3zyminq5_2q.png](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/5da22-1btneiw9tcxu3zyminq5_2q.png)

[I wrote an article about this topic](https://medium.com/dazn-tech/orchestrating-micro-frontends-a5d2674cbf33) a few months ago that I recommend reading if you are interested in this topic

# Communicating between Micro-Frontends

The final decision is finding a good way of communicating between micro-frontends bearing in mind that are independent units that should be completely decoupled between each other.
 When we decide to have multiple micro-frontends on the same page we need to decide how (and if) a micro-frontend should notify when an event or a user interaction happens for coordinating changes in other micro-frontends.
 In this case, we could use [custom events](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events) or an [event emitter library](https://github.com/ai/nanoevents) (there are many available) so our micro-frontends just emit/dispatch an event and who is listening reacts to that specific event.
 However, either we identify multiple micro-frontends per page or we consider a micro-frontend an entire view, we need to decide how a view would exchange data with another view.
 We can decide to use a web-storage solution (local and/or session storage) where every time a new view is loaded looks inside the web-storage to find the information needed or a [URL routing](https://martinfowler.com/articles/micro-frontends.html#Cross-applicationCommunicationViaRouting) where the application request to the backend the user state as displayed in the following diagram.

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/7924a-1b7xjuxbkvqn_q4za3v69lw.png](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/7924a-1b7xjuxbkvqn_q4za3v69lw.png)

As we have seen, there are some decisions to make when we want to approach a micro-frontends architecture, those will shape the other challenges will be faced alongside a micro-frontends project like how to create a seamless user experience, how to define our CI/CD pipelines, how to optimize our micro-frontends reducing our JavaScript bundles and so on.
 However, those 4 decisions made upfront will provide a strong guideline for facing all the other challenges and restrict the number of options to choose from.

# Last but not least…

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/3d977-1p16qxhvngcph0z3sg14thw.jpeg](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/3d977-1p16qxhvngcph0z3sg14thw.jpeg)

![Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/3d6ae-1o7yms_kptrehhmyce2jq5g.jpeg](Micro-frontends%20decisions%20framework%20542e05b347914f5295e57425eccef6eb/3d6ae-1o7yms_kptrehhmyce2jq5g.jpeg)

During my last keynote at [JS-Poland](https://js-poland.pl/) I introduced this decisions framework for the first time, you can find the slides here (hopefully the recording soon!) with some additional details.

I’d really like to hear your feedback about this decisions framework.
 If you feel it may help in your current or future projects if you think something is missing, if you think you have a better way to summarize a complex topic like this one, **please leave a comment**, I’m eager to read your point of view.

## Published by

### luca mezzalira

Being associated with the industry since 2004, I have lent my expertise predominantly in the solution architecture field. I have gained accolades for revolutionising the scalability of frontend architectures with micro-frontends, from increasing the efficiency of workflows, to delivering quality in products. My colleagues know me as an excellent communicator who believes in using an interactive approach for understanding and solving problems of varied scopes. I helped DAZN becoming a global streaming platform in just 5 years, now as Principal Architect at AWS, I'm helping our customers in the media and entertainment space to deliver cost-effective and scalable cloud solutions. Moreover, I'm sharing with the community the best practices to develop cloud-native architectures solving technical and organizational challenges. My core industry knowledge has been instrumental in resolving complex architectural and integration challenges. Working within the scopes of a plethora of technical roles such as tech lead, solutions architect and CTO, I have developed a precise understanding of various technicalities which has helped me in maximizing value of my company and products in my current leadership roles.  [View all posts by luca mezzalira](https://lucamezzalira.com/author/lucamezzalira/)