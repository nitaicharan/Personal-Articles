# Angular advanced features and tips you may not know about

Created: September 12, 2022 10:27 AM
Finished: No
Source: https://codereacter.medium.com/angular-advanced-features-and-tips-you-may-not-know-about-cae3b8527184
Tags: #angular

![Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1vhubvuriL0IXnhsxSDsTfg.jpeg](Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1vhubvuriL0IXnhsxSDsTfg.jpeg)

You’ve probably read a million articles with a similar title. I know I have. In this article I have compiled a list of features and tips of Angular which I love and which you should start using. So let’s go straight into it.

# Lazy loading components (not lazy loading routes)

First of all, lets get what lazy loading is — it means that you load the code for your component **only** when the component is actually needed. Think for instance about opening a dialog on a button click, ideally you should load the dialog component only when the user clicks the button since the user clicking the button is not guaranteed so there is no need to preload the component for the dialog.

The code for this incredibly simple.

![Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/15zHRZu5H0DTM-aYbhr_-aA.png](Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/15zHRZu5H0DTM-aYbhr_-aA.png)

That’s it! That is literally all it takes to lazy load a component. Now, let’s explain the code a bit:

- We are using the **from** function to transform a Promise into an Observable
- We are using the plain ol’ JavaScript **import.** There is nothing fancy or any other import function there. Just the JavaScript import function.
- The resulting **Observable** will emit once (so no need to store and manage the subscription) and it will emit a **Type<HelloWorldComponent>**, where **Type<T>** comes from **@angular/core**

Now let’s see a real life example of how you would lazy load a component to display in a dialog on a button click.

![Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1RHfBJj6vt8p1t9SQvHBHng.png](Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1RHfBJj6vt8p1t9SQvHBHng.png)

So we are using the **from/import** mechanism and then just opening a dialog using **exhaustMap** passing the resulting component to the **_dialog.open** function. Now let me share a gif with my network tab to show you that the HelloWorldComponent is bundled separately and loaded separately.

![Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1u1yxIYNjFyH4a9i9RznalA.gif](Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1u1yxIYNjFyH4a9i9RznalA.gif)

There are some pretty amazing use cases for lazy loading. One of which is dynamic content loading. Which is next on my list!

# Dynamic component rendering

Imagine you want to display a different component based on a condition. You will probably use ***ngIf** which is fine, if you have a very simple feature, but this is not always the case. What if you are working on a form generator based on a JSON, and you want to render different fields based on what the JSON describes, will ***ngIf or *ngSwitch** be ideal (or clean)? No, it will not.

In the following example I will have 2 components which I will use, **TextFieldComponent** and **SelectFieldComponent** with a button to toggle which one is displayed.

![Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1Zl_zABFFzIrCA7NqMKufWA.png](Angular%20advanced%20features%20and%20tips%20you%20may%20not%20kno%20a5c955f50190418ca86ef081a62c95ed/1Zl_zABFFzIrCA7NqMKufWA.png)

And the HTML code for the app.component is:

And seeing it in action.

The above code works, but it’s not ideal because now the main component is responsible for what is shows. To change this, we should create a wrapper component and do all the dynamic rendering there.

And then in the mai component we would use **app-field-host** instead.

And now let’s add the finishing touch — making **app-field-host** lazy load **SelectFieldComponent and TextFieldComponent** based on which one is displayed.

> Note that a component that has been lazy loaded will only get it’s JavaScript downloaded once, so if we use from/import again the JavaScript will not be reloaded since it is already in the browser.
> 

Now when **showTextField** setter is called the component needed component is lazy loaded and then displayed in the HTML as seen below in the **app-field-host** component.

Now the gif with my network tab to actually show you that the component is indeed loaded only when needed.

And voila! Now we are only loading components when we actually use them and dysplaying them dynamically.

> Theres an awesome library which is, in my opinion, better than the default Angular *ngComponentOutlet. Check it out here. I’ve been using it since forever and it is awesome as it doesn’t rely on custom injectors to pass data to child components, it does this out of the box by passing all @Input() and @Output() properties on the host component to the *ngxComponentOutlet marked component.
> 

# **Automatically unsubscribing in components**

This is an old issue people were trying to solve, and I think the most elegant solution is to use a **class decorator.**

## The problem

Those annoying subscriptions have to be unsubscribed from else we get that memory leak. What if I told you that you could have the component unsubscribe from them automaticall? And here’s the code for it.

## The solution

The above code is a decorator function, which means that it takes a class instance and alters it. In our case we are rewriting the **ngOnDestroy** function on the decorated class and whenever **ngOnDestroy** is called we are looking for all the properties on the class instance that have an **unsubscribe** property which is a function and we call the unsubscribe function on that property. Let’s see how you would use it.

Note that after the *altered ngOnDestry* is ran, the original ngOnDestroy gets called. This means that I can still have my own ngOnDestroy function and it will run just as smoothly.

Nice, clean, effective.

# Reducing super() calls when inheriting

This is a really annoying issue which luckily has a solution starting with Angular 14.

## The problem

In the **TextFieldComponent** class because we need the constructor for the **console.log** call we are forced to inject all 3 dependecies and pass them to the parent using the **super()** call. This is super annoying and if I have a lot of other classes that inherit from **FieldBaseComponent** I have to do that for them aswell should they need to run some code in their constructor. **Angular 14** to the rescue! 
 Angular 14 shipped with a new **inject()** function capabilities to be run in the constructor phase. This means that we can inject dependencies in the field initiator and we are no longer dependent on the constructor. I have a seperate article in which I get into more details about [how awesome the inject() function is](https://codereacter.medium.com/why-angular-14s-new-inject-function-is-so-amazing-ac281e7148d1).

## The solution

Nice and clean! I am no longer forced to inject 3 dependencies in **TextFieldComponent** yet if I were to use any of the 3 dependencies I would have access to them from the base class!

Give the **inject()** function a good readin’. You will love it.

## Final thoughts

That’s it for now, I will try to come back with a few more tips on improving the quality of Angular code.

Thanks for reading!