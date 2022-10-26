# Interceptors

Created: October 21, 2021 12:18 PM
Finished: Yes
Finished 📅: November 22, 2021
Source: https://docs.nestjs.com/interceptors
Tags: #nestjs

- [x]  [Interceptors](https://docs.nestjs.com/interceptors#interceptors)
- Injectable()
- implement the `NestInterceptor`

![Untitled](Interceptors%20cfa7399cfcaf4ebe876e88bf1333c2b2/Untitled.png)

- Each interceptor implements the `intercept()` method
- Arguments: `ExecutionContext` and `CallHandler`
- [x]  [Basics](https://docs.nestjs.com/interceptors#basics)
- [x]  [Execution context](https://docs.nestjs.com/interceptors#execution-context)
- [x]  [Call handler](https://docs.nestjs.com/interceptors#call-handler)
- `intercept()` method effectively **wraps** the request/response stream
- may implement custom logic **both before and after** the execution of the final route handler
- **before** calling `handle()`
- `handle()` returns a RxJS `Observable`
- If an interceptor which does not call the `handle()` method is called anywhere along the way the handle controller won't be executed
- [x]  [Aspect interception](https://docs.nestjs.com/interceptors#aspect-interception)

```tsx
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    console.log('Before...');

    const now = Date.now();
    return next
      .handle()
      .pipe(
        tap(() => console.log(`After... ${Date.now() - now}ms`)),
      );
  }
}
```

- [x]  [Binding interceptors](https://docs.nestjs.com/interceptors#binding-interceptors)
- In order to set up the interceptor, we use the `@UseInterceptors()` decorator
- interceptors can be controller-scoped, method-scoped, or global-scoped
- Global interceptors cannot inject dependencies since this is done outside the context of any module
- [x]  [Response mapping](https://docs.nestjs.com/interceptors#response-mapping)
- [x]  [Exception mapping](https://docs.nestjs.com/interceptors#exception-mapping)
- [x]  [Stream overriding](https://docs.nestjs.com/interceptors#stream-overriding)
- [x]  [More operators](https://docs.nestjs.com/interceptors#more-operators)