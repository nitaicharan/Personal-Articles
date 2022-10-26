# Create angular components dynamically based on backend api responses

Created: August 12, 2022 9:03 AM
Finished: No
Source: https://medium.com/@aniket.muruskar/create-angular-components-dynamically-based-on-backend-api-responses-58a9734502eb
Tags: #angular

![Create%20angular%20components%20dynamically%20based%20on%20bac%20b8fefc08a25f48629eaad112f7fc233a/12YEIuL4XL-4vD-G-YU3poQ.jpeg](Create%20angular%20components%20dynamically%20based%20on%20bac%20b8fefc08a25f48629eaad112f7fc233a/12YEIuL4XL-4vD-G-YU3poQ.jpeg)

# Introduction

This article will introduce you how to create dynamic angular components based on the backend api responses. It will covers the basic dynamic component templates creation.

> This article is quite in-depth, based on the dynamic component loader, please check out Angular Dynamic component loader documentation.
> 

Component templates are not always keep static in design. An application might need to load new dynamic templates without tightly coupling the fixed reference in the component templates.

# Prerequisites

If you would like to follow along with this tutorial.

> Note: This tutorial was verified with latest @angular/core v14.0.0 and @angular/cli v14.0.0
> 

# Host directive

Before the kick-start, you have to define an anchor point that tells where to render all dynamic components in the DOM.

Let’s say you have a **AppComponentHostDirective.** In the **@Directive** decorator, selector name **appDynamicComponentHost;** that we will use to apply directive to the element.

**Host Directive**

In above **AppComponentHostDirective** directive which injects **ViewContainerRef** into constructor to gain access for the view container of the element that will host the dynamically added component.

# **Dynamic components container**

> Most of the dynamic components loading functionality is in app-ui-builder.component.ts.
> 

**UI Builder Component Template**

The **<ng-template>** element is where you apply the directive you just made in **app-component-host.directive.ts.** Now Angular knows the container where to dynamically load components.

# App component

**App Routing**

# App routing module

Please take a closer look at **[app-routing.module.ts](https://gist.github.com/aniketmuruskar/10ce164b7094d1ad423db22ddcc8f73c)** module.

The routing module defines two routes called ***/project-overview,* */project-detail*.** Here each route using resolver guard to fetch data before route activation i.e **AppResolver**.

**App Routing Module**

> Note: For each route using the same AppDynamicComponent, inside the template passing components list from AppResolver as @input() to app-ui-builder.component.ts
> 

**Routing Component Template**

# App resolving page

The **AppResolver,** a angular service that can be used with router to resolve data during navigation. The **Resolve** interface defines a **resolve()** method that is invoked when navigation start.

**App Resolver**

> How AppResolver loads component data
> 
> 
> 1. The **resolve()** get invoked automatically when navigation start.
> 
> 2. Get current activated route url from **ActivatedRouteSnapshot** i.e line 24.
> 
> 3. Make http call for fetching data using **HttpClient** i.e line 31.
> 
> 4. Build page based on data received from the api using **PageBuilderService** i.e line 37.
> 

# Page builder service

All components implements a common **AddComponent** to define the API for passing data to the components.

**Page Builder Service**

> Note: Inside buildPage() method, looping through component list, which in turns calling getPageElement() method which returns AddComponent instance by setting respective component i.e via parameters based on componentType.
> 

# Route listeners

Inside **AppDynamicComponent,** using **ActivatedRoute** service to get information from a route inside **ngOnInit()** method and track **page** parameter (i.e as key) at resolved data from the **AppResolver**, and assigned it to the **components** array.

> Note: Here binding components array as input to app-dynamic.component.html
> 

# Page builder component

Most of the dynamic component loading implementation is in [app-ui-builder.component.ts](https://gist.github.com/aniketmuruskar/72f64af6c68a8dc4980fd6b0416f258a).

**UI Builder Component**

> How Page builder component works
> 
> 
> 1. **AppUIBuilderComponent** takes an array of **AddComponent** as a input, which ultimately comes from **PageBuilderService** i.e line 11.
> 
> 2. The **buildComponents()** doing a heavy job here, like clearing view container, setting input configuration and adding components. It’s calling inside **ngOnInit**, **ngOnChanges** life-cycle hooks methods.
> 
> 3. **componentHost** refers to the directive which created earlier to tell Angular where to load dynamic components, recall **AppComponentHostDirective** injects **ViewContainerRef** into its constructor. Using this approach, directive accesses the element to host the dynamic component.
> 
> 4. To add component to the template, call **createComponent()** on directive **viewContainerRef** which resolve instance of loaded component, then we can make configuration for component.
> 

# Page responses format

In this article, used sample ***json*** responsesfrom the **assets/pages** directory. Here each object inside **pageElements** is the configuration for the component **componentType** property defines component type like label, text-box, text-area, dropdown, checkbox, radio.

> Note: We can modify the configuration based on use cases
> 

# Configuration for API Url

Inside **[app.resolver.ts](https://gist.github.com/aniketmuruskar/4ca47ec447d971d2c4b0dd3bd599572b)** file, uncomment the **fetchServerResponse** when to use actual backend response configuration and commented out **fetchLocalResponse** . Here **pageName** defines api url path name like **project-overview, project-detail** etc.

Inside **[environment.ts](https://gist.github.com/aniketmuruskar/2c48d4670ff6be70de23554cbb1a2cda#file-environment-ts)** file, mention backend domain api url. Currently pointing to **assets/pages** for the demonstration.

# Conclusion

In this article, used [Angular Dynamic component loader](https://angular.io/guide/dynamic-component-loader) functionality to **create basic dynamic component creation** based on the backend responses. All data and information provided in this article are for informational purposes only.

For more customization, we can add more features like **bootstrap layout for component, nested component, form submission, validation** according to use cases.

Please checkout the full codebase: [https://github.com/aniketmuruskar/dynamic-component-app.git](https://github.com/aniketmuruskar/dynamic-component-app.git)

I hope this article will be useful. If you have any questions, don’t hesitate.

Thanks for reading.