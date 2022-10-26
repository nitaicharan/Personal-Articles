# Loading data before components in Angula

Created: September 15, 2022 10:19 AM
Finished: No
Source: https://tpiros.dev/blog/loading-data-before-components-in-angular/
Tags: #angular

[share-card](Loading%20data%20before%20components%20in%20Angula%2008e1f5e371094214a8df6c61ca4b2e44/share-card)

This post is 4 years old. (Or older!) Code samples may not work, screenshots may be missing and links could be broken. Although some of the content may be relevant please take it with a pinch of salt.

In this article, we'll review a few ways to make sure that data is available for a component before we load it and display it in the application to the user.

# Setup the app

Let's set up an application using the angular-cli first - notice the usage of the routing flag - this will enable us to have a routing module added to the project by default: `ng new route-change-app --routing`.

Let's also add two new components via the CLI: `ng g c home && ng g c products`.

We also need to update `app-routing.module.ts` and add our routes:

```
// excerpt
import { HomeComponent } from './home/home.component';
import { ProductComponent } from './about/product.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'products', component: ProductComponent },
];
```

Last but not least let's open `app.component.html` and add some the following HTML:

```
<nav>
  <a routerLink="/">Home</a> |
  <a routerLink="/products">products</a>
</nav>
<router-outlet></router-outlet>
```

# A slow API

Under normal circumstances in a full-stack JavaScript application, we are going to interact with an API of some sort. This means that the speed of our application is also dependent on how fast the API is returning data.

For demonstration purposes let's mimic an API that is "slow". Let's create a service via the CLI and call it `ApiService`: `ng g s api`.

> 
> 
> 
> To keep things simple we'll not use an actual backend for the API, but instead, we'll add some static data via an Observable and a delay.
> 

The API service should look like this:

```
import { Injectable } from '@angular/core';
import { of, Observable } from 'rxjs';
import { delay } from 'rxjs/internal/operators';

@Injectable({
  providedIn: 'root',
})
export class ApiService {
  constructor() {}

  getProducts(): Observable<any> {
    const products = [
      { name: 'shoe', price: 15.99 },
      { name: 'shirt', price: 25.99 },
      { name: 'jeans', price: 54.5 },
    ];
    return of(products).pipe(delay(3000));
  }
}
```

It is time to consume this API from the `ProductComponent`:

```
import { ApiService } from '../api.service';
// ... more code
constructor(private api: ApiService) { }
products;
ngOnInit() {
  this.api.getProducts().subscribe(products => this.products = products);
}
```

And finally, display it in the component template as well:

```
<ul>
  <li *ngFor="let product of products">
     costs
  </li>
</ul>
```

This is a pretty straightforward setup. Now if we launch the application, we can see that navigation to the "products" route will display a blank page for 3 seconds, after which the data will arrive and will be displayed.

This is not ideal because for 3 seconds the user doesn't have a clue what's happening. It could very well be that the user assumes that the site is not working correctly and will navigate away. Not an ideal scenario.

Let's take a look at what options we have for indicating that the page/route is loading.

# Option 1: `ngIf`

One of the most straightforward options that come to mind is to have an `ngIf` statement added to the component template which indicates a loading status - until the `this.products` variable doesn't get populated with data, we show a loading indicator:

```
<p *ngIf="!products">Loading data ...</p>
<ul>
  <li *ngFor="let product of products">
     costs
  </li>
</ul>
```

> 
> 
> 
> Note that when accessing properties of an object that didn't load a [Safe Navigation Operator](https://fullstack-developer.academy/question-mark-in-angular-expression/) should be used.
> 

# Option 2: Router resolver

Another option that can be implemented is called a router resolver. As the name suggests, we can add a resolve function to the route which loads the component that has an API call to do. This will cause the component to be only loaded and displayed by Angular once the API call (or whatever else we define) is loaded.

Let's see how this can be implemented.

The first thing will be to create a separate class which we'll have the resolver functionality, and again, let's use the Angular CLI to create it: `ng g class resolver`.

The class should have the following content:

```
// resolver.ts
import { Injectable } from '@angular/core';
import { Resolve } from '@angular/router';
import { Observable } from 'rxjs';
import { ApiService } from './api.service';

@Injectable()
export class Resolver implements Resolve<Observable<string>> {
  constructor(private api: ApiService) {}

  resolve() {
    return this.api.getProducts();
  }
}
```

Let's not forget to add this class as a provider:

```
// app.module.ts
import { Resolver } from './resolver';
// ...
providers: [Resolver],
```

The code above implements the `Resolve` interface - which requires us to have a `resolve()` method in place. In that method, we consume the API created earlier in this example.

The other change that we need to make is in `app-routing.module.ts` since we need to tell our route definition where and how to resolve the data:

```
// app-routing.module.ts
import { Resolver } from './resolver';

const routes: Routes = [
  { path: '', component: HomeComponent },
  {
    path: 'products',
    component: ProductsComponent,
    resolve: { products: Resolver },
  },
];
```

And there's one more final change that we need to make. Remember that at the moment we make an API call in the product component as well as in the resolver class that we just added - this is not required, there shouldn't be duplicate API calls. Therefore we'll go ahead and change product component.

The resolved data is available on the router snapshot, which we can access from `@angular/router`. So let's set this up:

```
// products.component.ts
import { ActivatedRoute } from '@angular/router';
// ...
constructor(private route: ActivatedRoute) { }
products;
ngOnInit() {
  this.products = this.route.snapshot.data.products;
}
```

Now when refreshing the application, and clicking the Products link, we'll see a delay before the route changes. Once the API resolves the call the route changes, and we see the list of products.

# Conclusion

We had a look at a few ways to handle situations when a potential API call would slow down our application. Make sure to implement one of these in order to avoid users navigating away from the site.