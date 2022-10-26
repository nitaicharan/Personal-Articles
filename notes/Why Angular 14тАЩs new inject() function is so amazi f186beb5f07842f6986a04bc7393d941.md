# Why Angular 14’s new inject() function is so amazing?

Created: September 12, 2022 10:27 AM
Finished: No
Source: https://codereacter.medium.com/why-angular-14s-new-inject-function-is-so-amazing-ac281e7148d1
Tags: #angular

As you have probably already read, Angular 14 shipped some very interesting features: typed forms (which can still be improved, see how [here](https://codereacter.medium.com/how-to-improve-angular-14-reactive-forms-typings-1a9963139023)), standalone components, and, the focus of this article, the ability to use the inject( ) function during the so-called constructor phase.

Let’s break the above sentence in 2.

## What is the inject() function

The inject() function takes an InjectionToken as a parameter and returns the value for that InjectionToken from the currently active injector. Basically, it’s another way to get a hold of a dependency other than using constructor injection. Here’s an example.

![Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/1SKsLpN6DmgdUxKE_6nannQ.png](Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/1SKsLpN6DmgdUxKE_6nannQ.png)

Simple use of the inject() function

Might seem like “it’s different, but the same”, but bear with me. You will see how powerfull this new mechanism really is.

## What is the constructor phase

The constructor phase means the constructor function scope and field initializers. This means that you cannot call the inject() function inside @Input() setters or any other function or lifecycle hook. Here is an example to better understand it.

![Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/1230Q7vbvzAegt4achdW2qA.png](Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/1230Q7vbvzAegt4achdW2qA.png)

Good, now that we understood the basics let’s see what the fuss is about with a few simple examples.

> Note that you can use the inject() function in Pipes and Directives aswell, double awesme!
> 

# Reducing component complexity with inject()

Imagine that you need a router path param in your component in order to fetch an entity details from the API.

Let’s see how that looked without using inject() function (or any other facade service for that matter).

![Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/1iuad5bT32OlDbCStrNMkcw.png](Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/1iuad5bT32OlDbCStrNMkcw.png)

The above example is the classic way of doing things, you would probably use a facade service to do the API calls, but other than that this should feel pretty familiar.

What’s wrong with the above code:

1. You depend on the HttpClient (or other facade service that does the API call).
2. You depend on the ActivatedRoute.
3. You have business logic in the component which should only be in charge of showing UI, not worring about how to get the data.

Let’s see the improved version by using the inject() function.

![Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/14ihbADP4CSMgoghnb94mfg.png](Why%20Angular%2014%E2%80%99s%20new%20inject()%20function%20is%20so%20amazi%20f186beb5f07842f6986a04bc7393d941/14ihbADP4CSMgoghnb94mfg.png)

Fetching an entity from the API using inject()

In the above code the component is oblivious to what is required to get the entity from the server. All it knows is that it now has a property named entity$ which stores the Observable<Entity>. No constructor injection needed at all. This allows components to be components instead of being components + data aggregators.

> In a normal application you would probably have had a facade service to extract some of the data fetching logic out of the component, at least that was the best practice. But if you use the inject() function you dont even need a service as a dependency in your component, you just need to use fetchEntity().
> 

## What if I want to do the API call on a button click?

The inject() function has you covered here aswell. First off, let’s see what you cannot do.

The above code will throw a runtime error when the refreshEntity method is called outside of the constructor phase, on a button click for example, since fetchEntity uses inject(). It will not throw this error when used to initialize the entity$ property since that IS in the constructor phase.

So how can we fix this?

By using the good ol’ JavaScript closure.

In the above example, because we use a closure, we are able to store the injected HttpClient and ActivatedRoute inside the closure scope and still use values in the returned function. Thus we can leverage the inject() function outside the constructor phase.

# Facading using inject()

As you already saw in the above examples, we were able to keep the component clean and dependency injection free. We no longer depended on the HttpClient or the ActivatedRoute directly in the component constructor. We instead created an injectable function (thats how I like to call them) that takes care of the business logic, similar to how we would extract the logic into a Facade service which we would (still) inject in our component. Using the inject() function we no longer need any dependencies in our component.

Another use case would be to facade out the Store when using redux with ngrx.

No facading of Store

The above example is the worst case scenario, you are depending directly on the Store which is a 3rd party library. A logical move would be to Facade this logic into a service in order to protect yourself from the library deprecation or breaking changes.

Using facade

A little better. This is how we would normally do things before Angular 14. More code, but more resilience to breaking changes since we now only have to manage code in one place, the EntityFacade, instead of everywhere we would use the Store.

Now let’s see the inject() way.

Some people will say that using closures demands storing the returned function in the component class. It increases complexity so they mightaswell use constructor injection and services. Thats totally fine, there’s no right way of doing things, only what you and your team chose. However, look how clean the component looks without any constructor dependency injection. It’s cleaner, more UI focused.

Let’s see now how we can combine the fetch and select parts of the above example in a single injectable function for the ultimate cleanup.

Here we combined the dispatch and select functions of redux in a single inject() function to further reduce the amount of code we have to write.

# Removing super() calls

Ah, inheritance, how awesome and lousy you are.

I think we all had a case where we had a component which inherited another base component. For example, if we created custom inputs which share functionality. We would probably move that shared functionality in a base class and inherit it. But if we have a lot of dependencies in that base class then we need to call that lousy super() function and pass it the required dependencies. That sucks and I personally hate doing it. Let’s see a simple example.

In the above example, because we need to inject OtherService in the constructor we MUST inject the ChangeDetectorRef since it’s injected in the class we are inheriting, even if we would not use it. Here’s how the inject() function helps us with this.

And, of course, the last step would be to remove the constructor alltogheter and just use private _service = inject(OtherSerivice) and to create closures for detectChanges.

# Conclusion

The new inject() function is awesome, and I think the community will do some pretty amazing things with it. I expect every major library to create injectable functions in order to avoid directly injecting their exported services in your components while also providing you with the service should you chose the classic constructor injection way.

Having worked with react I feel like the inject function works a lot like the react hooks which were a highly acclaimed and appreciated new feature of react.

Thank you!