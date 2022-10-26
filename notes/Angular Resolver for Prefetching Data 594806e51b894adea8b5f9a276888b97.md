# Angular Resolver for Prefetching Data

Created: September 15, 2022 10:10 AM
Finished: No
Source: https://javascript.plainenglish.io/angular-resolver-for-prefetching-data-angular-guards-resolve-40fda257d666
Tags: #angular

![Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1XPQoqucjRoq5ct0xCYP_Ng.png](Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1XPQoqucjRoq5ct0xCYP_Ng.png)

Guard Types in Angular, Angular Resolve Guard

# What is Angular Resolver?

Angular Resolvers are used in order to pre-fetch some data while the user is redirecting from one Route to another. The newly available page will already have the data that is required to be rendered in the page.

In certain scenario, we might require to pre-load the data for the component to be displayed, so that during the initial rendering itself the component contains the data rather than displaying the empty component first and then querying for API to extract the data.

# Why do we use Resolvers in Angular Route?

![Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1nLP9mRCr8HkpLfn3LWFq-Q.png](Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1nLP9mRCr8HkpLfn3LWFq-Q.png)

The common use case scenario for using Angular Resolvers is when the route which is getting loaded is looking for some asynchronous data. The asynchronous data need to be displayed in the component.

There are 2 approaches when we have some requirement for asynchronous data for the Component to be rendered.

1. **Loading Data after Component Loads**: In the first approach, we can initially render the component without any data, once the component is loaded, we can make an Ajax Call to extract the component data. Then the component is re-rendered to display the data available from the Ajax Call. Its similar to rendering a blank template initially and then adding data to the template. The data might be available after a short delay once component is initially rendered.
2. **Loading Data before Component Loads**: Another approach is to keep data handy before rendering the component. If the data is needed to be extracted from a Asynchronous call, the component will wait first for the data to be available before rendering the component for the first time. this way, we wont be rendering the empty template ever. The router is waiting for data to be available first before initial rendering of the component.

# How to create Angular Resolver Service

![Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1B-SMgwbL1iWBcwmhrjPpdw.png](Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1B-SMgwbL1iWBcwmhrjPpdw.png)

How to create Angular Resolve Service

In order to implement Resolver Service, we need the following:

1. We need to import Resolve Interface from “**@angular/router**”
2. Create a service class implementing this **“Resolve” Interface**
3. **Override “resolve()” function** to specify the HTTP request on which we need to wait. This function might return a Promise or Observable
4. Add this Service class to **“root” module**.
5. Inject “**ActivatedRouteSnapshot**” to the Component that is loaded to use the data available from the AJAX request specified in “resolve” function

# Implement Resolver Service

![Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1yGLpqV-s9n6lpnw8BXmI1A.png](Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/1yGLpqV-s9n6lpnw8BXmI1A.png)

Implementing Resolver Service in Angular

Lets create a “**DataService**” to extract the data for “**EmployeeListComponment**”. This service would fetch the list of Employee for the company from a simple API call. Below is the implementation…

Lets start with Implementing the Service that would resolve the data that need to be available to the component before it loads to the screen.

[https://gist.github.com/Mayankgupta688/67fca46dbbfc47c9289c45dd6be41f1f](https://gist.github.com/Mayankgupta688/67fca46dbbfc47c9289c45dd6be41f1f)

In the above code, we have created a “DataService” **implementing the “Resolve” Interface**. Since we are implementing the interface, we need to **define a “resolve” function**, which can **return a Promise/Observable or Object Data** from the Function. Now we can use this service to prefetch the data before the component is loaded on the Application Page.

# Implement Routes in Angular Module

![Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/12dHWY_-5QFE5vETrZxu8Aw.png](Angular%20Resolver%20for%20Prefetching%20Data%20594806e51b894adea8b5f9a276888b97/12dHWY_-5QFE5vETrZxu8Aw.png)

Adding routs to Angular Module

Once we have created the Service, we need to configure our routes to specify the data that is needed to be prefetched for each component. Let’s see, how can we modify the routes…

[https://gist.github.com/Mayankgupta688/64c5f3e7f7ff49cbbd6fa97f2234bc01](https://gist.github.com/Mayankgupta688/64c5f3e7f7ff49cbbd6fa97f2234bc01)

The above code defines the routes for the application. While we are defining the routes for the application, we provide a key “resolve” containing an object to be used for extracting the data. “Resolve” key is assigned with an object having “employee” as a key and “DataService” as a value.

**“resolve” function of “DataService” is invoked** and the **returned Promise/Observable is resolved first** before loading the Component. The data extracted from the API is assigned to the object “**employees**”. The data can be extracted inside the component using the following code.

# Subscribing to the Resolved Data

[https://gist.github.com/Mayankgupta688/66b3059fb0352b2f29088315a368c2f5](https://gist.github.com/Mayankgupta688/66b3059fb0352b2f29088315a368c2f5)

In order to get the resolved data in the application, we need to inject “**ActivatedRoute**” to the constructor. Since the “DataService” resolve function is returning the Observable. We can **subscribe to the “this._routes.data”**, this will resolve and return us all the data that is specified in the “resolve” key of the routes specified above.

The above code will return us list of employee from the API specified in “DataService” Class. The list of employees will be available in “employees” key once the data is subscribed.

The working code is given below:

[https://codesandbox.io/s/angular-resolve-routing-qoh9h?file=/src/app/app.module.ts](https://codesandbox.io/s/angular-resolve-routing-qoh9h?file=/src/app/app.module.ts)