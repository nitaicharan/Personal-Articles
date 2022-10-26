# Async/Await in Angular ngOnInit using TypeScript Decorator

Created: July 18, 2022 6:47 AM
Finished: No
Source: https://javascript.plainenglish.io/async-await-in-angular-ngoninit-using-typescript-decorator-b8b35963407c
Tags: #angular

![Async%20Await%20in%20Angular%20ngOnInit%20using%20TypeScript%20D%20f92505dbfeeb4c5db68243729a4c0e9b/1Eipu9kkYnoP8ahM1LbDwgQ.jpeg](Async%20Await%20in%20Angular%20ngOnInit%20using%20TypeScript%20D%20f92505dbfeeb4c5db68243729a4c0e9b/1Eipu9kkYnoP8ahM1LbDwgQ.jpeg)

Lots of times, there is a need to load data using Promises from API before the page loads or Class initialization.

To achieve this I see many of my fellow devs use `async` on `ngOnInit` so they can `await` on data fetch API method inside `ngOnInit`

```tsx
async ngOnit {
  this.movies = await this.service.getMovies();
}
```

But if you see carefully there is no one awaiting `ngOnInit` and you can’t `await ngOnInit` even if you wanted to.

It will run the async function but it WILL NOT await for it to complete, it just allows you to use the `await` keyword, but it is not a `aysnc` function, despite having the `async` keyword.

It looks kind of awkward and somewhat hard to comprehend, doesn’t seem conventional.

Ideally, the way is to use route resolvers so that data is loaded before the route finishes navigating and I can know the data is available before the view is ever loaded.

A lot of Stack Overflow answers point to a more readable way of doing it without adding `async` keyword to `ngOnInit` but the solution behaves the same.

```tsx
ngOnInit() {
  (async () => {
    this.movies = await this.service.getMovies();
  });
}
```

And this is not just for Angular components. Imagine a simple class that loads some data and also has getters and setters for that data.

```tsx
class MovieService {
  private movies: Movie[];
  constructor(){
    this.loadMovies();
  }
  getMovieById(id: number) {
    return this.movies.find(movie => movie.id === id);
  }
  getAllMovies() {
    return this.movies;
  }
  private async loadMovies(){
    this.movies = await MovieStore.getTodos();
  }
}
```

In above example, the data will start loading immediately when the instance of the class is created and the constructor can’t be blocked until the data is fetched completely. So if getters are called while the request is still pending, it might end up in default empty or `undefined` movies object.

So you can only hope that the data will be loaded by the time any getters are called. Turn around for this is to add some lazy loading and make accessors await the load request something like below.

```tsx
async getMoviesById(id: number){
  if(!this.movies){
    await this.loadMovies();
  }
  return this.movies.find(movie => movie.id === id);
}
```

Looks good right, but this code now becomes a duplicate code and the load request might be fired more than once only if we could find a way to abstract all this wiring with something else.

A *Decorator* is a special kind of declaration that can be attached to a [class declaration](https://www.typescriptlang.org/docs/handbook/decorators.html#class-decorators), [method](https://www.typescriptlang.org/docs/handbook/decorators.html#method-decorators), [accessor](https://www.typescriptlang.org/docs/handbook/decorators.html#accessor-decorators), [property](https://www.typescriptlang.org/docs/handbook/decorators.html#property-decorators), or [parameter](https://www.typescriptlang.org/docs/handbook/decorators.html#parameter-decorators).

Short and crisp, a **decorator is** **a function that takes another function and extends the behavior of the latter function without explicitly modifying it.**

A good way to abstract the wiring would be using decorators somewhere we can wait for called all methods which are to be run and completed first, and then run the ones which are dependent on those. Something like below:

```tsx
class MoviesService {
    private movies: Movies[];

    @waitForInit
    async getMoviesById(id: number) {
        return this.movies.find(user => user.id === id);
    }

    @waitForInit
    async getAllMovies() {
        return this.movies;
    }

    @init
    private async loadMovies() {
        this.users = await myMoviesStore.getUsers();
    }
}
```

Here’s how to make these decorators work. You can add each decorator to as many methods as you’d like.

```tsx
// Code credit goes to @RomkeVdMeulen Follow him on github https://github.com/RomkeVdMeulen

const INIT_METHODS = new Map<any, string[]>();
const INIT_PROMISE_SYMBOL = Symbol.for("init_promise");

type InitMethodDescriptor = TypedPropertyDescriptor<() => void>
    | TypedPropertyDescriptor<() => Promise<void>>;

export const init = (target: any, key: string, _descriptor: InitMethodDescriptor) => {
    if (!INIT_METHODS.has(target)) {
        INIT_METHODS.set(target, []);
    }

    INIT_METHODS.get(target)!.push(key);
}

export const waitForInit = (target: any, _key: string, descriptor: PropertyDescriptor) => {
    const method = descriptor.value!;

    descriptor.value = function (...args: any[]) {
        if (!Object.getOwnPropertySymbols(this).includes(INIT_PROMISE_SYMBOL)) {
            if (!INIT_METHODS.has(target)) {
                this[INIT_PROMISE_SYMBOL] = Promise.resolve();
                return;
            }

            const promises = INIT_METHODS.get(target)!.map(methodname => {
                return Promise.resolve(this[methodname]());
            });

            this[INIT_PROMISE_SYMBOL] = Promise.all(promises);
        }

        return this[INIT_PROMISE_SYMBOL].then(() => method.apply(this, args));
    };
    return descriptor;
}
```

## Code explanation for those who are new to Decorators

To understand the init/waitForInit decorator code we first must understand how the decorators are evaluated.

> TypeScript documentation on Decorators here does a wonderfull job of explaing how decorators are evaluated, I will recommend checking it.
> 

Let’s take the below example to understand the evaluation:

```tsx
function first() {
  console.log("first(): factory evaluated");
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("first(): called");
  };
}
 
function second() {
  console.log("second(): factory evaluated");
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("second(): called");
  };
}
 
class ExampleClass {
  @first()
  @second()
  method() {}
}

/**
 * Which would print this output to the console:
 * 
 * first(): factory evaluated
 * second(): factory evaluated
 * second(): called
 * first(): called
 */
```

Same as above, all our `init` decorated methods would be evaluated first, where in the movies service case, `loadMovies` would be registered as one of the `INIT_METHODS` and then when any one of the methods decorated with `waitForInit` like `getAllMovies` would be called then it would first run the `loadMovies` Promise first and then run the corresponding calling function.

> Wait 🤔 We were talking about 'async ngOnInit' where is the code? 👊
> 

Don’t worry, we got you covered. Here is how you can use the same `init/waitForInit` decorator on `ngOnInit` .

Here is the Stackblitz example demonstrating the said decorators on `ngOnInit`.

[https://angular-ivy-kq23e9.stackblitz.io](https://angular-ivy-kq23e9.stackblitz.io/)

```tsx
import { Component } from '@angular/core';
import { init, waitForInit } from './init';

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent {
  movies: any[];
  @init
  private async loadMovies() {
    this.movies = await Promise.resolve([
      'Toy Story',
      'Bahubali',
      'Terminator',
      'Iron Man',
    ]);
  }

  @waitForInit
  ngOnInit() {
    console.log('Initializing view template with pre-loaded data', this.movies);
  }
}

/**
 * Which would print this output to the consoles:
 * 
 * Now initializing view template with preloaded data
 * ["Toy Story 3", "Bahubali", "Terminator", "Bob the Builder"]
 * 
 */
```

Concluding, we briefly discussed the implementation on pre-loading the data in any class or before template initialization in `ngOnInit` in Angular using the TypeScript decorator.

Please leave a comment below and let me know various other ways, if any, to do so or if there is any improvisation. I will be happy to address them.

**References:**

1. [Forcing Angular to Wait on Your Async Function](https://dev.to/jdgamble555/forcing-angular-to-wait-on-your-async-function-2ck1)
2. [@init / @waitOnInit TypeScript method decorators](https://romkevandermeulen.nl/2018/05/10/waitoninit.html)
3. [TypeScript Decorators](https://www.typescriptlang.org/docs/handbook/decorators.html#decorator-composition)
4. [[StackOverFlow] async/await in Angular `ngOnInit](https://stackoverflow.com/questions/56092083/async-await-in-angular-ngoninit/58474585#58474585)`

*More content at **[PlainEnglish.io](https://plainenglish.io/)**. Sign up for our **[free weekly newsletter](http://newsletter.plainenglish.io/)**. Follow us on **[Twitter](https://twitter.com/inPlainEngHQ)** and **[LinkedIn](https://www.linkedin.com/company/inplainenglish/)**. Check out our **[Community Discord](https://discord.gg/GtDtUAvyhW)** and join our **[Talent Collective](https://inplainenglish.pallet.com/talent/welcome)**.*