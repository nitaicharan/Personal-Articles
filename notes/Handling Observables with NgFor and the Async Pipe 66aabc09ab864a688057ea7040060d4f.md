# Handling Observables with NgFor and the Async Pipe

Created: May 7, 2022 11:35 PM
Finished: No
Source: https://ultimatecourses.com/blog/angular-ngfor-async-pipe
Tags: #angular

![angular-ngfor-async-pipe-10c7dc4c677ad8bab8c787f20aed30076718c583b24fcbb122027b559fc871ce.png](Handling%20Observables%20with%20NgFor%20and%20the%20Async%20Pipe%2066aabc09ab864a688057ea7040060d4f/angular-ngfor-async-pipe-10c7dc4c677ad8bab8c787f20aed30076718c583b24fcbb122027b559fc871ce.png)

Now you’ve learned the basics of [Angular’s NgFor](https://ultimatecourses.com/blog/angular-ngfor-template-element) it’s time to take things up a notch and introduce some Observables. In this article you’ll learn how to use Observables with Angular’s NgFor directive and the async pipe.

NgFor has a not-so-obvious feature that lets us will help us deal with asynchronous operations - the async pipe. The async pipe takes care of subscribing/unsubscribing to Observable streams for us.

Let’s explore how we can handle NgFor alongside the async pipe to subscribe to an Observable!

### Using ngFor

Here’s the data we’ll be using as our source to pass into `*ngFor`:

```
interface Pizza {
  id: string;
  name: string;
  price: number;
}

const getPizzas$: Pizza[] = [
  { id: "j8P9sz", name: "Pepperoni", price: 899 },
  { id: "tMot06", name: "Supreme", price: 999 },
  { id: "x9sD3g", name: "Sizzler", price: 899 },
];

```

And here’s where we introduce NgFor alongside perhaps another local [NgForOf variable](https://angular.io/api/common/NgForOf#local-variables) such as `index`:

```
<ul>
  <li *ngFor="let pizza of pizzas; index as i">
    {{ i + 1 }}. {{ pizza.name }}
  </li>
</ul>

```

But Angular is *reactive*, this means it’s all about Observables. This pattern of Angular development doesn’t follow an Observable data source pattern. So, let’s bring them in!

### ngFor and Async Pipe

For this, we’ll introduce [Observable of()](https://www.learnrxjs.io/learn-rxjs/operators/creation/of) from RxJS and demonstrate subscribing to an Observable stream via the async pipe and update our typings:

```
import { Observable, of } from "rxjs";

const getPizzas$: Observable<Pizza[]> = of([
  { id: "j8P9sz", name: "Pepperoni", price: 899 },
  { id: "tMot06", name: "Supreme", price: 999 },
  { id: "x9sD3g", name: "Sizzler", price: 899 },
]);

```

> 
> 
> 
> 🕵️‍♀️ RxJS `of()` is an Observable creation operator that emits the value we pass in!
> 

Let’s get the `getPizzas$` variable into our component:

```
@Component({
  selector: 'app-root',
  template: `...`
})
export class AppComponent implements OnInit {
  pizzas$: Observable<Pizza[]>;

  constructor() {}

  ngOnInit() {
    this.pizzas$ = getPizzas$;
  }
}

```

> 
> 
> 
> 🔭 Tip: it’s common practice to suffix properties that are Observables with a `$`, such as `pizzas$`
> 

Our NgFor directive can now use the async pipe to subscribe to the `pizzas$` observable:

```
<ul>
  <li *ngFor="let pizza of pizzas$ | async; index as i">
    {{ i + 1 }}. {{ pizza.name }}
  </li>
</ul>

```

It’s important to remember that using `| async` creates a subscription if the source is an Observable. Thankfully, when the component is destroyed the subscription is also managed and unsubscribed for us!

Check out the live StackBlitz example:

## 🎉 Download it free!

Ready to go beyond ForEach? Get confident with advanced methods - Reduce, Find, Filter, Every, Some and Map.

As an extra bonus, we'll also send you some extra goodies across a few extra emails.

### NgFor Template and Async Pipe

When we think of NgFor, we should think of [NgForOf](https://angular.io/api/common/NgForOf). Here’s how you can use NgFor with the `<ng-template>` element using the async pipe:

```
<ul>
  <ng-template ngFor [ngForOf]="pizzas$ | async" let-pizza let-i="index">
    <li>{{ i + 1 }}. {{ pizza.name }}</li>
  </ng-template>
</ul>

```

This is the longer syntax using `<ng-template>` and explains why we use `*ngFor` with the asterisk, which denotes that it’s a *behavioral directive*.

Check out the live StackBlitz example:

### NgFor + NgIf + Async Pipe

So far we’ve covered some really nice usage of NgFor with the async pipe, but now we’re going to show a common practice of “unwrapping” observables on different levels of the template.

First, using [NgIf and the Async Pipe](https://ultimatecourses.com/blog/angular-ngif-async-pipe), we can do this:

```
<div *ngIf="pizzas$ | async as pizzas">
  {{ pizza }}
</div>

```

Instead of using the async pipe directly with NgFor, we could use NgIf to unwrap our Observable into a `pizzas` variable.

This would allow us to reuse the variable *multiple times* inside the template without creating repeat subscriptions:

```
<div *ngIf="pizzas$ | async as pizzas">
  <p>🍕 {{ pizzas.length }} Pizzas!</p>
  <ul>
    <li *ngFor="let pizza of pizzas; index as i">
      {{ i + 1 }}. {{ pizza.name }}
    </li>
  </ul>
</div>

```

This also means we can avoid using things like the safe navigation operator `?` with NgIf or NgFor.

Check out the live StackBlitz example:

### Summary

We’ve covered a lot of ground here using Angular’s NgFor directive and the async pipe.

Starting with static data we then used Observable `of()` to create an Observable and async pipe it into NgFor.

From there, we looked at how we could potentially use NgIf alongside NgFor (and the rest of our template) to avoid subscribing multiple times to our Observable.

> 
> 
> 
> If you are serious about your Angular skills, your next step is to take a look at my [Angular courses](https://ultimatecourses.com/courses/angular) where you’ll learn Angular, TypeScript, RxJS and state management principles from beginning to expert level.
> 

Happy coding!