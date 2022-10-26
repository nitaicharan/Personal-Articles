# How to implement the circuit breaker pattern in Node.js

Created: November 28, 2021 5:24 PM
Finished: Yes
Finished 📅: November 28, 2021
Source: https://webera.blog/como-implementar-o-pattern-circuit-breaker-no-node-js-965b2fb39ee6
Tags: #nodejs

Hello everyone, I hope you are all ok, today we will see a very interesting subject that will allow us to analyze the behavior of our applications and intervene in case of anomalies. We will implement a very well-known design pattern called a circuit breaker.

Before starting the implementation, let’s understand what a circuit breaker is and why to apply this design pattern.

![How%20to%20implement%20the%20circuit%20breaker%20pattern%20in%20No%204570d7bf4f6c48bd867883c0d1ff1b72/1efegVXhadwnJkvQwu__QTQ.jpeg](How%20to%20implement%20the%20circuit%20breaker%20pattern%20in%20No%204570d7bf4f6c48bd867883c0d1ff1b72/1efegVXhadwnJkvQwu__QTQ.jpeg)

# What is Circuit Breaker?

I believe that you are already familiar with the word circuit breaker this is a very common term when we have a house and we need to connect several household appliances on the same circuit breaker happens that if the network overloads it happens to trip, cutting off the power of all appliances that connect to it, to restore power you will need to turn the circuit breaker back on again.

This pattern is similar when working with services connected on the same network and need to communicate. We need to reduce this impact when we have a service that is running slowly or that is down. The circuit breaker monitors these possible faults, when they reach a certain threshold, the circuit “disarms” and any call made after that returns an error or we can adopt a fallback response. Then, after a set amount of time, the circuit breaker makes calls again to the affected services. If the calls are successful, the circuit closes and traffic starts flowing again.

In his book Release It: Design and Deploy Production-Ready Software, the author [Michael Nygard](https://twitter.com/mtnygard) uses this analogy and advocates a design pattern applied to the above problem. The process flow behind this is very simple:

- If a call fails, increase the number of failed calls by one.
- If the number of failed calls goes above a certain threshold, open the circuit.
- If the circuit is open, immediately return with an error or standard response.
- If the circuit is open and some time has passed, open the circuit halfway.
- If the circuit is half open and the next call fails, open it again.
- If the circuit is half open and the next call is successful, close it.

## And why is this partner important to our application?

Implementing a circuit breaker is important when multiple services communicate and depend on each other. If a service fails, it could crash our entire application or leave an important part down.

# Creating our Circuit Breaker

There are some libraries and many ways to implement our Circuit Breaker, as our goal is to understand the implementation and how it works we will reinvent the wheel to see how it works.

Before getting down to business, let’s understand what we’re going to develop:

- A server using very simple Express.js that will act as our upstream resource and simulate successfully and failed requests.
- A class from our circuit breaker that uses the Axios library to make requests and have the ability to log data.

For this example, we are going to create we are using TypeScript to implement these features.

![How%20to%20implement%20the%20circuit%20breaker%20pattern%20in%20No%204570d7bf4f6c48bd867883c0d1ff1b72/0KQcOKcIFjrB-7RGU.gif](How%20to%20implement%20the%20circuit%20breaker%20pattern%20in%20No%204570d7bf4f6c48bd867883c0d1ff1b72/0KQcOKcIFjrB-7RGU.gif)

Let’s start our development by running the command below in the project directory:

```
$yarn init -y
```

After execution it will generate a **package.json** file, so we can already install our main dependencies like **Axios** and **Express.Js**.

```
$yarn add express axios
```

As we are going to work with Typescript we need to install the necessary dependencies:

```
$yarn add typescript
$yarn add -D ts-node @types/node @types/express @types/axios
```

Create **tsconfig.json**, required for tsc and ts-node, to compile **TypeScript** for **JavaScript**:

```
$yarn tsc --init --rootDir src --outDir ./build --esModuleInterop --lib ES2019 --module commonjs --noImplicitAny true
```

Let’s add compile and run scripts to package.json. Add the following code snippet to the package.json file:

```json
"scripts": {
  "build": "tsc",
  "start": "node ./bin/app.js",
  "dev": "ts-node ./src/app.ts"
},
```

Now we can run our code more easily.

With our dependencies installed and the ts file configured, the next step is to create our server with **Express.JS**, create a folder at the root called src and add a file called **app.ts** inside add the code snippet below:

```tsx
import express, { Request, Response } from "express";

const app = express();
const port = 8080;

app.get( '/', (req: Request, res: Response) => {

    if ( Math.random() > 0.5 ) {
        res.status( 200 ).send( "Success!" );
    } else {
        res.status( 400 ).send( "Failed!" );
    }

});

app.listen( port, () => console.log( `Listening at http://localhost:${ port }` ) );
```

The code above will raise a server with express that will be receiving requests of type **GET** on localhost with port **8080** it may fail responding with status **400** or succeed by issuing status **200**.

Phew, now that we have the server, let’s finally start creating our circuit breaker.

Create a folder called **circuit-breaker** to better organize our application structure.

Obs: Execute the command below inside the **src** folder.

Let’s define the states of our circuit breaker, we’ll use an **ENUM** and some colors to identify each one of them to facilitate our development.

```tsx
export enum BreakerState {
    GREEN = "GREEN",
    RED = "RED",
    YELLOW = "YELLOW"
}
```

We need to configure some settings for our circuit breaker like:

- How many failures will be allowed before going to the **Red state**, we can define as our **failureThreshold class.**
- How many success requests will be passed to the **Green state**, we will call this **class successThreshold.**
- When our request is in **Red state**, how long should we wait until a new request is allowed? This class will be represented by **timeout**.

In the code snippet below we will create a Public Class that will be called BreakerOptions which will have the properties we defined above.

```tsx
export class BreakerOptions { 
  constructor(
    public failureThreshold: number,
    public successThreshold: number,
    public timeout: number
  ){}
}
```

With our options and states defined, let’s create a Class called CircuitBreaker, in the beginning, we install Axios to perform requests so it will be the dependency we will use in our Class.

```tsx
import { BreakerOptions } from "./BreakerOptions";
import { BreakerState } from "./BreakerStates";
import axios, { AxiosRequestConfig } from "axios";

class CircuitBreaker {
    private request: AxiosRequestConfig;
    private state: BreakerState;

    private failureCount: number;
    private successCount: number;

    private nextAttempt: number;

    // Options
    private failureThreshold: number;
    private successThreshold: number;
    private timeout: number;

    constructor(request: AxiosRequestConfig, options?: BreakerOptions) {

        this.request        = request;
        this.state          = BreakerState.GREEN;

        this.failureCount   = 0;
        this.successCount   = 0;
        this.nextAttempt    = Date.now();

        if ( options ) {
            this.failureThreshold   = options.failureThreshold;
            this.successThreshold   = options.successThreshold;
            this.timeout            = options.timeout;
        } else {
            // Define defaults
            this.failureThreshold   = 3;
            this.successThreshold   = 2;
            this.timeout            = 3500;
        }
    }

}
```

Let’s understand the code above and the defined properties:

- We define a property called a request that will contain details of our request, we define the **AxiosRequestConfig** as the request configuration defined in the **Axios library**.
- The **state** property will keep the state of our circuit breaker. Earlier we created the **BreakerState class.**
- The **failureCount** will be our failure counter.
- The **successCount** as opposed to **failureCount** which will track our successes.
- **nextAttempt** will store the date and time to perform attempts when we are in **Red state**.

But we don’t stop there, we now need to refine our methods. They will be defined as:

- log — a method that will record the status of our circuit breaker and can be used with our monitoring system (spoiler of what comes in the next articles) 😁
- exec — it will be a public API to which we will trigger the request attempts, it will be an asynchronous function because we need to wait for a response from the server. It works: if the status is **Red** and the next attempt is scheduled sometime in the future, throw an error and abort. If the status is **Red** but the timeout period has expired, the status will change to **Yellow** and we allow the request to pass. If the status is not **Red** we try to make the request and based on the **success** or **failure** of the request we call the appropriate method.
- success — this method will handle successful responses. If it is in the **Yellow state**, we increase the success count — and if the success count is greater than the defined threshold, it will reset and move to the **Green state**.
- failure — different from the above method this will handle the failures. If the failure count exceeds the threshold, go to the red state and define when our next attempt should occur.

```tsx
private log(result: string): void {

        console.table({
            Result: result,
            Timestamp: Date.now(),
            Successes: this.successCount,
            Failures: this.failureCount,
            State: this.state
        });
    }

    public async exec(): Promise<void> {

        if ( this.state === BreakerState.RED ) {

            if ( this.nextAttempt <= Date.now() ) {
                this.state = BreakerState.YELLOW;
            } else {
                throw new Error( "Circuit suspended. You shall not pass." );
            }
        }

        try {
            const response = await axios( this.request );

            if ( response.status === 200 ) {
                return this.success( response.data );
            } else {
                return this.failure( response.data );
            }
        } catch ( err: any ) {
            return this.failure( err.message );
        }
    }

    private success(res: any): any {

        this.failureCount = 0;

        if ( this.state === BreakerState.YELLOW ) {
            this.successCount++;

            if ( this.successCount > this.successThreshold ) {
                this.successCount = 0;
                this.state = BreakerState.GREEN;
            }
        }

        this.log( "Success" );

        return res;

    }

    private failure(res: any): any {

        this.failureCount++;

        if ( this.failureCount >= this.failureThreshold ) {
            this.state = BreakerState.RED;

            this.nextAttempt = Date.now() + this.timeout;
        }

        this.log( "Failure" );

        return res;
    }
```

Now that we’ve completely finished our class, let’s automate our test by creating a script that will invoke our circuit breaker class, calling the exec method we defined at a 1-second interval.

Create a file called index.ts inside the src folder and add the code below:

```tsx
import { CircuitBreaker } from "./circuit-breaker/CircuitBreaker";
import { BreakerOptions } from "./circuit-breaker/BreakerOptions";

const circuitBreaker = new CircuitBreaker({
  method: "get",
  url: "http://localhost:8080"
}, new BreakerOptions( 3, 5, 5000 ) );

setInterval(() => {
    circuitBreaker
        .exec()
        .then( console.log )
        .catch( console.error )
}, 1000 );
```

Then add the following script to **package.json** to execute our file created in the example above:

```json
"circuit-breaker": "npm run build && node ./build/index.js"
```

# Result

![How%20to%20implement%20the%20circuit%20breaker%20pattern%20in%20No%204570d7bf4f6c48bd867883c0d1ff1b72/1tmkqhY3nJ-pZGCUiECeTTQ.png](How%20to%20implement%20the%20circuit%20breaker%20pattern%20in%20No%204570d7bf4f6c48bd867883c0d1ff1b72/1tmkqhY3nJ-pZGCUiECeTTQ.png)

# Conclusion

Our goal was to show how we can implement this pattern, we created one from scratch to understand every step by step of the implemented logic, but this method will not always be scalable for your team, it is worth analyzing your application’s architecture well before implementing it, as I said at the beginning of the article that there are other ways to work with circuit breaker.

So what did you think of our article? If you liked it, leave your comment so that we can improve and bring new articles of what we are learning and applying in our daily lives.