# Inject dynamic content in Angular components with portals

Created: August 8, 2022 3:34 AM
Finished: Yes
Finished 📅: August 8, 2022
Source: https://blog.logrocket.com/inject-dynamic-content-angular-components-with-portals/
Tags: #angular

![Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/rendering-dynamic-content-angular-components-using-angular-cdk-portal.png](Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/rendering-dynamic-content-angular-components-using-angular-cdk-portal.png)

As an Angular developer, a large part of our daily job is putting components together to build our app. From time to time, we will need to inject a component or UI template into another component dynamically.

In Angular, there are a couple of ways to render dynamic components into a host component before the CDK portals are released. These are:

Both methods have a drawback: the host component needs to reference the injected component directly. The coupling between the host and injected component makes it hard to test and maintain.

Portal provides a flexible and clean alternative method of injecting content into an Angular component.

Let’s walk through an example of using Portal step-by-step.

## Defining the problem

Let’s say we’re enhancing the dashboard screen in [an Angular app](https://blog.logrocket.com/tag/angular). The screen contains

- A parent component (`dashboard`): this is the container of the dashboard screen
- A dropdown selector: the change of selection will change the context of the dashboard
- A router outlet: this is used to load the components from subroutes
- Two subcomponents (the `stats` and `action` components): these display information related with current context of dashboard

When the user picks an option from the selector, the app will navigate to different subroutes. As a result, the corresponding component will be loaded into the router outlet. The two subcomponents will then be updated with different content.

Upon the selection change, the selected service type data is pushed into the `serviceType$` observable in the `DashboardService`. In the `action` component, we subscribe to the `serviceType$` observable.

```
// TypeScript// dashboard.component.ts// when user change a selection, new serviceType are broadcastedthis.service.searchType$.next(serviceType);// dashboard.service.tsexport class DashboardService {
  searchType$ = new BehaviorSubject('');
  constructor() { }}// action.component.tsexport class ActionComponent {
  serviceType$ = this.service.searchType$;
  constructor(private service: DashboardService) {}}
```

We use `ngSwitch` to react to the observable `serviceType$`. In the following example, the `action` component content is updated when the observable value changes.

```
// actions.Component
          <div class="panel-body" *ngIf="serviceType$ | async as serviceType">
          <div [ngSwitch]="serviceType">
            <div *ngSwitchCase="'client'">
              <button (click)="registerNewClient()" class="btn btn-primary">
                Register New Client
              </button>
            </div>
            <div *ngSwitchCase="'order'">
              <button (click)="registerNewOrder()" class="btn btn-danger">
                Search order
              </button>
            </div>
            <div *ngSwitchDefault>default action</div>
          </div>
        </div>
```

The dashboard works, but there are a couple of problems with the code:

### More great articles from LogRocket:

- Don't miss a moment with [The Replay](https://lp.logrocket.com/subscribe-thereplay), a curated newsletter from LogRocket
- Switch between [multiple versions of Node](https://blog.logrocket.com/switching-between-node-versions-during-development/)
- [Learn how to animate](https://blog.logrocket.com/animate-react-app-animxyz/) your React app with AnimXYZ
- [Explore Tauri](https://blog.logrocket.com/rust-solid-js-tauri-desktop-app/), a new framework for building binaries
- Compare [NestJS vs. Express.js](https://blog.logrocket.com/nestjs-vs-express-js/)
- [Discover](https://blog.logrocket.com/best-typescript-orms/) popular ORMs used in the TypeScript landscape

### The two subcomponents are smart components

These subcomponents are designed to present data, so they should be dumb, or presentational, components. Instead, the current design makes them aware of the external data entities

### The subcomponents also contain side effects

This means that they register event handling, which makes them hard to be reused. To add a new service type, we need to add `ngSwitchCase` into all of our subcomponents, and with more service types or subcomponents being added, the dashboard will become more complex and harder to maintain. What we want is to inject UI contents into the subcomponents while the subcomponents don’t know where the contents are coming from.

## How can portals help?

Portals are provided as part of the [Angular Material CDK](https://material.angular.io/cdk/categories), which is internally developed by the Angular Material team. Its name was recently shortened to [Angular CDK](https://github.com/angular/components/tree/master/src/cdk). The definition of portals in the [official documentation](https://github.com/angular/components/blob/master/src/cdk/portal/portal.md) is: portal is a piece of UI that can be dynamically rendered to an open slot on the page. There are two key parts:

- `Portal`: the UI element (component or template) to render. It can be a component, a `templateRef` or a DOM element.
- `PortalOutlet`: the slot where the content will be rendered. In the previous version, it was called `PortalHost`.

Let’s make use of the portals to solve the above problem.

## Setting up

To start using the Angular CDK portal, the following package needs to be installed.

```
npm install @angular/cdk
```

Then in the `app.module.ts` file, we need to import the CDK module.

```
// TypeScriptimport {PortalModule} from '@angular/cdk/portal';
```

## Types of portals

We have a few options to create a portal.

- `ComponentPortal`: create a portal from a component type.
    
    ```
    // TypeScriptthis.portal = new ComponentPortal(StatsComponent);
    ```
    
- `TemplatePortal`: create a portal from a `&lt;ng-template>`
    
    ```
    // Html<ng-template #templatePortal>
      <ng-content></ng-content></ng-template>
    ```
    
- `DomPortal`: create a portal from a native DOM element. This allows us to take any DOM element and inject it to the host

With `DomPortal`, the Angular binding within the content won’t be effective because it’s treated as a native DOM element.

Angular CDK also provides a `[cdkPortal directive](https://material.angular.io/cdk/portal/api#CdkPortal)`, which is a version of `TemplatePortal`. The `cdkPortal` directive saves some boilerplate code compared to `TemplatePortal`, as we don’t need to manually instantiate the portal.

## Create a portal

In this example, we use the `cdkPortal` directive because it’s simpler and more declarative.

As the below code shows, we wrap `ng-content` inside the `ng-template` in the `ActionButtonComponent` template. Then, we add the `portal` directive into the `ng-template`.

There are two equivalent selectors for the `cdkPortal` directive: `portal` or `cdk-portal`. `ng-content` is used so we can project contents from the other components.

```
// Html
// ActionButtonComponent
 <ng-templatecdk-portal>
    <ng-content></ng-content>
 </ng-template>
```

Please note that the element with the `cdkPortal` directive will not be shown until it’s attached to `CdkPortalOutlet`. This applies to all elements, including `div`.

In the `ActionButtonComponent` class, we can reference the template using the `@ViewChild` and `CdkPortal` directives.

```
// TypeScript// ActionButtonComponent
  @ViewChild(CdkPortal)
  private portal: CdkPortal;
```

## Creating the `PortalOutlet`

In the `ActionComponent`, we created a placeholder with the ID set to `action`.

```
// Html
// ActionComponent
 <divid="action"></div>
```

Now we can create the `DomPortalOutlet`. We use `document.querySelector` to get hold of the DOM element placeholder defined above. The rest of the parameters are injected via the component constructor.

Please note that the `DomPortalOutlet` was previously called `DomPortalHost`. Since Angular 9, it’s been renamed to `DomPortalOutlet`.

```
// Html
// ActionButtonComponent

  private host: DomPortalOutlet;

  constructor(
    private cfr: ComponentFactoryResolver,
    private appRef: ApplicationRef,
    private injector: Injector
  ) {}

  ngAfterViewInit(): void {
    this.host = new DomPortalOutlet(
      document.querySelector('#action),
      this.cfr,
      this.appRef,
      this.injector
    );
```

The creation of `DomPortalOutlet` occurs within the `ngAfterViewInit` [lifecycle event](https://blog.logrocket.com/angular-lifecycle-hooks/). It’s necessary because `ngAfterViewInit` occurs right after the view is rendered.

## Putting them together

After both `portal` and `DomPortalOutlet` are defined, we can attach the portal to the `portalOutlet`. This will inject the portal into the placeholder referenced by the `portalOutlet`.

```
// TypeScript// ActionButtonComponentexport class ActionButtonComponent implements AfterViewInit, OnDestroy{

  @ViewChild(CdkPortal)
  private portal: CdkPortal;
  private host: DomPortalOutlet;

  constructor(
    private cfr: ComponentFactoryResolver,
    private appRef: ApplicationRef,
    private injector: Injector
  ) {}

  ngAfterViewInit(): void {
    this.host = new DomPortalOutlet(
      document.querySelector('#action),
      this.cfr,
      this.appRef,
      this.injector
    );
    this.host.attach(this.portal);
  }}
```

In this case, both the client and order components can project content into `ActionButtonComponent`. Those contents are shown in the `portalOutlet` in `ActionComponent`.

```
// Html
// client.component.html
<app-action-button>
  <button (click)="registerClient()"class="btn btn-primary">Register New Client</button></app-action-button>
```

Here is an overview of how the portal and `portalOutlet` work together.

![Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/how-portal-portaloutlet-work-together.png](Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/how-portal-portaloutlet-work-together.png)

## `detach` method vs. `dispose` method

We use the `detach` method to remove the previously attached portal from the `portalOutlet`. It’s to clean things up when the parent component is removed.

Another way is to use the `dispose` method. When calling dispose, we permanently remove the `portalOutlet` from DOM.

```
// TypeScript// ActionButtonComponent
ngOnDestroy(): void {this.host.detach();}
```

In our example, we use the `detach` method, as our intention is to detach the portal instead of removing the `portalOutlet` from DOM.

## Passing context instead of contents

In the previous example, we don’t need to pass data because `ng-content` is used to project contents. But for other use cases, you may need to pass contextual data into the portal.

To pass context data in `templatePortal`, we can use the `context` property.

```
// TypeScriptthis.portal.context = {}; // Your context data
```

For `ComponentPortal`, we can use token injection shown in the code below.

```
// TypeScript// create a custom tokenexport const CONTEXT_TOKEN = new InjectionToken({...});// when creating the componentPortal, provide the token injectorconst injector = Injector.create({
providers: [{ provide: CONTEXT_TOKEN, useValue: {...}, // context data variable});const portal = new ComponentPortal(ComponentClass, null, injector);//Inject the token into the constructor of the component, so it can be accessedconstructor(@Inject(CONTEXT_TOKEN) private data: T)
```

## Final results

Below is what the final result looks like. Our dashboard shows dynamic content when the dropdown selector changes. Best of all, the subcomponents (the `stats` and `action` components) are loosely coupled. They do not contain logic about clients or orders; instead, they only need to focus on rendering the content correctly.

![Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/final-results-dashboard.gif](Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/final-results-dashboard.gif)

## Summary

In this article, we discussed how to use Angular CDK portals to inject dynamic contents to a few components in a dashboard. You can find [the full example code](https://github.com/sunnyy02/cdk-portal) on my GitHub.

The CDK Portal is a powerful feature. Its major benefits include its flexibility and clean separations. It gives us the ability to “teleport” content to any component within the screen, even if it’s outside the current component tree.

I hope this article can help you to apply this technique in your own awesome app!

## Experience your Angular apps exactly how a user does

Debugging Angular applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking Angular state and actions for all of your users in production, [try LogRocket](https://www2.logrocket.com/angular-performance-monitoring).

[https://logrocket.com/signup/](https://www2.logrocket.com/angular-performance-monitoring)

![Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/610d6a7-687474703a2f2f692e696d6775722e636f6d2f696147547837412e706e67.png](Inject%20dynamic%20content%20in%20Angular%20components%20with%20%20ec7d9e10d2f94bf7a00b64ec7df9b809/610d6a7-687474703a2f2f692e696d6775722e636f6d2f696147547837412e706e67.png)

[LogRocket](https://www2.logrocket.com/angular-performance-monitoring) is like a DVR for web and mobile apps, recording literally everything that happens on your site including network requests, JavaScript errors, and much more. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred.

The LogRocket NgRx plugin logs Angular state and actions to the LogRocket console, giving you context around what led to an error, and what state the application was in when an issue occurred.

Modernize how you debug your Angular apps - [Start monitoring for free](https://www2.logrocket.com/angular-performance-monitoring).