# Application Architecture for Angular Projects

Created: September 26, 2022 8:53 AM
Finished: No
Source: https://betterprogramming.pub/application-architecture-for-angular-project-17de7a1fbb8a
Tags: #angular

## This article is compiled from the documentation I’ve been creating for projects I lead. You can use it as a source of ideas or as a guide.

![Application%20Architecture%20for%20Angular%20Projects%20eebe727f38b74b90a969a171cb1a23e7/1yyrcATao06JA3ev3o3QpWg.jpeg](Application%20Architecture%20for%20Angular%20Projects%20eebe727f38b74b90a969a171cb1a23e7/1yyrcATao06JA3ev3o3QpWg.jpeg)

# Repository

[Nx](https://nx.dev/) will significantly improve DX. It is quite simple to start using and it’s incredibly powerful. Even if you will use it just for one project in a repository (you don’t have to move all of your projects and use it as a monorepo — it’s optional), Nx will still help you with libraries hot-reload, generators of e2e boilerplate, caching of builds and tests.

# State Control

The application should have a global state (AppState), implemented using [NgRx Store](https://ngrx.io/guide/store) or [ComponentStore](https://ngrx.io/guide/component-store).

Some feature modules (usually mini-apps), if they need it, might have their own state (FeatureState), implemented using NgRx Store or a [ComponentStore](https://ngrx.io/guide/component-store) declared as global (`providedIn: 'root'`).

Most of the components, excluding the “[dumb components](https://medium.com/@thejasonfile/dumb-components-and-smart-components-e7b33a698d43)”, should have their own ComponentStore.

> If a component can be dumb (only inputs and outputs, no internal data or state modification) — it’s better to make it dumb. They are easier to test and reuse.
> 

If some “smart” component can be split into a group of dumb components — it’s better to do this. Smart components should handle all their events (such as button clicks, file uploading, mouse events, and so on) using their ComponentStore effects. All the business logic should be inside the ComponentStore. Angular components themselves should have a minimal amount of code — it will make state management much easier, as well as unit testing and code reuse.

To transfer data to the components, prefer to use `@Inputs` or effects of a store. Do not use global injectable services as storage - it will have all the [flaws of global variables](http://wiki.c2.com/?GlobalVariablesAreBad). Even if you’ll use some BehaviourSubject, it will not prevent the data pollution and related side effects (or even worse - some part of the app functionality will be based on these side effects). There are good reasons why the [Redux pattern](https://redux.js.org/understanding/thinking-in-redux/three-principles) is successful, and why a state object should be immutable.

To transfer data out of components, use `@Output` in simple cases and store effects/actions when you need to broadcast your data to higher levels than just a parent component.

Data received from the API and targeted to some component should be stored in the ComponentStore, not in an AppStore or FeatureStore — when a component is destroyed, its store is also destroyed and memory can be freed.

# Structure

In the “app” folder, we should have only the wireframe of the app — root-level routing, loaders of lazy modules, and AppState control. In this text, under “components” I mean “components, directives, and pipes”.

Every feature (independent or semi-independent part of the app) should be placed in a library. With time, features will reuse each-others components, and it will be much easier to import them from a library. Later, some components will migrate from that library to the “components” library.

> Every component should be a standalone component.
> 

It will improve their reusability and testability significantly. The benefits will be especially noticeable when some components will be moved to a different library, and all the tests will keep working.

Every feature module should be lazy-loaded.

# Testing

A complete [guide about testing projects with NgRx](https://timdeschryver.dev/blog/testing-an-ngrx-project) is written by one of the NgRx core maintainers, Tim Deschryver.

For E2E testing, I recommend [Playwright](https://playwright.dev/). For libraries of components, [Storybook](https://storybook.js.org/) might be handy.

# Classes

> Prefer composition to inheritance!
> 

Fields and methods that will be used outside of class (not in the template, in case of component) should be declared as`public`. Cases, when you need public fields, are extremely rare.

Fields and methods of the component, accessible from the template, should be declared as `protected`.

All the other fields and methods should be declared as `private` (including fields, declared in the constructor). The majority of fields and methods will be `private`.

Public fields are part of API, so it is important to distinguish them for the refactoring — `private` and `protected` fields and methods can be safely renamed/removed, that's why every field and method that should not be exposed, should be declared as `private` or `protected`.

> Use the readonly keyword if you don’t expect the field to be modified - it helps a lot.
> 

Not only to catch the attempts to redefine this field, but also to refactor the code safely. Observables, subjects, sets, and maps — examples of obviously readonly fields.

# Components

> Every component should use changeDetection: OnPush.
> 

Constructors should be as minimal as possible — they often can not be monitored or overridden in tests. Components with the context-specific logic in constructors are often impossible to reuse.

Avoid using `ngOnInit()` in components (although it is ok to use some initialization method in component stores to remove logic from constructors and improve testability).

In dumb components, it's just not needed.
In smart components, when initialization logic is declared in `ngOnInit()`, a component often becomes non-responsive to the changes in inputs - it will update the state only once. Even if `ngOnChanges` will be implemented, it’s not the best way of tracking changes.

It is better to use this pattern to update the state on every change of the input:

A component should not be huge — huge components are less reusable, more fragile, and have to check more expressions on every change detection cycle.

Component’s methods should be just wrappers, transferring an event to the store (local or global). Avoid non-trivial logic here, make them as tiny as possible.

Avoid using functions in the template — it’s better to use pure pipes (if transformation is needed) or store selectors (if the value should be calculated only once per data change).

# Component Stores

For testing purposes, please declare some initialization method (name doesn’t matter) to move all the logic out of the constructor (and call this method in the constructor). You can use [ComponentStore Lifecycle hooks](https://next.ngrx.io/guide/component-store/lifecycle) also if you want.

When declaring effects, prefer to use [concatLatestFrom()](https://ngrx.io/api/effects/concatLatestFrom) instead of `withLatestFrom()`.

If at some point you want to call an effect from an effect — consider moving that functionality that you want to call into a private method.
Otherwise, you’ll create a nested subscription, and it’s never a good thing.
Example:

In the second case, if registrationSuccess$ will be canceled for some reason, a regCounterRequest call will be canceled as well.
This rule is just an inheritance of the rule “avoid nested subscriptions”. There is a linter rule for this, but it can’t detect nested subscriptions when you call them inside the sub-functions. So in case of effects, it’s pretty easy to create nested subscriptions — please avoid it.

# Services

Injectable services with “providedIn: root” are global singletons — they were brought to Angular by Miško Hevery, so it would be quite entertaining to read this article from him: [Singletons are Pathological Liars](http://misko.hevery.com/2008/08/17/singletons-are-pathological-liars/).
This contradiction might look weird, but there are two facts: 1) Miško Hevery is right about singletons; 2) you can avoid all the negative consequences if you make them stateless.
Every time you create a service with `@Injectable({providedIn: 'root/platform'})`, make it stateless.

Such services should not have public fields, should not have writeable private fields, and should not use `this` keyword in their methods to mutate data.

If you need to inject a configuration object into your service, declare them using injection tokens:

This way, you can declare `UPLOADER_CONFIG` without importing `UploaderConfig` class - in many cases it allows you to don’t break lazy-loading even if you need to configure providers before lazy-loading.

# API & Data Access

Component Stores should access API endpoints only using services, located in the “api” library (API Services). Components should not have any knowledge about API endpoints or API data structures.

# API Services should

- Access the API endpoints;
- Cache the data when possible;
- Invalidate cache by expiration time and on every write/delete request.

# Data Flow

```
Some event on the page (a button click, or just a page loading)
                ⬇️A component sends this event to its ComponentStore
                ⬇️In the “effect” function, we are sending a request to the API Service method
                ⬇️API Service method returns an observable
                ⬇️Whenever this observable emits a value (new data), we are updating the state
                ⬇️The component's template is subscribed to the state (using async pipe)
                ⬇️Data (from the state) is rendered in the template on every update
```

# Component state modifications

There are multiple reasons to modify the state of a component.
For example, when your component is displaying a table of data, there are multiple modifications you might have, that should not affect the source data — modifications of the representation.
Changing the order of columns; modifying the list of currently rendered rows (virtual scrolling); pre-save data edits; canceling the edit.
In the case of movable items or interactive graphs, there are even more possible modifications that you want to reflect in the state, without modifying the source data.

That’s why API Service should be the only source of truth when we want to get the data, and we should not pollute it with our filters, formatters, and so on. All these modifications should be applied to a state stored in a component store. And when this state is modified, the store’s selectors will return updated data.

Some modifications will not modify the data itself (changing the order of columns) — in such cases, we just need to update the state, our data selector should watch for this and return the new version of data:

Other modifications will require modifications of the users in the example above: if we want to edit some users’ data (email, phone).
We still should not pollute the data, received from the API Service, because such edits might be canceled or are just temporary by their nature (for example, when items are moved on the screen).

# Naming conventions

Libraries will get “src” and “lib” sub-folders generated automatically. Please put your code in the “lib” folder.

Service libraries, inside the “lib” folder, should have folders “services”, “models” (if needed), and “shared” (if needed).

Every service should have its own folder inside the “services” folder. Here you can put the tests and `README.md` file (if needed).

Components/pipes/directives should have their own folder each, in the “lib” folder.
This folder will contain the template, styles, tests, and documentation (if needed).
If the name of a component is “example”, then this folder will be created (some files are [optional]):

```
libs/feature/src:
└─ lib
    ├─ example
    │     ├─  example.component.html
    │     ├─  [example.component.scss]
    │     ├─  example.component.spec.ts
    │     ├─  example.component.ts
    │     ├─  [example.store.ts]
    │     └─  [README.md]
    └─ [README.md]
```

Models (interfaces, classes) should have 1 file per model, model files might be grouped in folders if it makes sense (if there are obvious reasons for such grouping, and it doesn’t decrease their visibility).

Do not use Hungarian notation (“I” prefix in interfaces).

Do not use TypeScript namespaces for grouping the models.

# Documentation

Even the perfect code should have some documentation.
Of course, writing documentation is boring, and keeping it updated is not easy.

That’s why you should not add documentation to every component, but consider creating some small descriptions for a library, feature, or complex component. One README file with 3 lines of text might give much more information than hours of searching through Jira issues.

Also, it’s easier to add some info to an *existing* README file, than create a *new* one from scratch.
So when creating a new library or a module, please create some `README.md` containing at least a single word.