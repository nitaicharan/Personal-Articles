# Avoiding Circular Dependency Issues in Nest.js

Created: June 6, 2022 4:41 PM
Finished: No
Source: https://betterprogramming.pub/nest-js-avoid-circular-dependency-issues-4f7577377acc

## A quick look at circular dependency issues with the progressive Node.js framework

[Avoiding%20Circular%20Dependency%20Issues%20in%20Nest%20js%20b6d78493748b4dc889db5477131500fd/0mWazAexWx5lA5rzN](Avoiding%20Circular%20Dependency%20Issues%20in%20Nest%20js%20b6d78493748b4dc889db5477131500fd/0mWazAexWx5lA5rzN)

Photo by [Tine Ivanič](https://unsplash.com/@tine999?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

A [circular dependency](https://docs.nestjs.com/fundamentals/circular-dependency) occurs when two classes depend on each other.

For example, class A needs class B, and class B also needs class A. Circular dependencies can arise in Nest between modules and between providers.
While circular dependencies should be avoided where possible, you can’t always do so.

In such cases, Nest enables resolving circular dependencies between providers in two ways. In this chapter, we describe using forward referencing as one technique and using the `ModuleRef` class to retrieve a provider instance from the DI container as another.

# Our Example Case

We have 2 classes the `UsersRepo` (in `UsersModule`) and the `JwtService` (in `AuthModule` provided by `JwtModule` declared in `AuthModule`). And each one depends on the other.

The `UsersRepo` of the `UsersModule` needs the `JwtService` (`JwtModule`) for JWT validation and decoding. The `JwtModule` is declared, configured and exported in `AuthModule`. So, in `UsersModule` we can (we have to) import it the ‘normal’ way.

Here’s the code for the `UsersModule`:

We can inject the `JwtService` the ‘normal’ way in the `UsersRepo` constructor:

```
import { JwtService } from ‘@nestjs/jwt’;
. . .
export class UsersRepo {
constructor(private jwtService: JwtService), …)
```

From the other side, the `AuthService` of the `AuthModule` needs the UsersRepo for user signin/signup. `UsersRepo` is a class (a service provider) exported by `UsersModule`. We can (we have to) import it in `AuthModule` using the `forwardRef()`.

The `AuthModule`

Again, we can also inject the `UsersRepo` in the `AuthService`, by using the ‘normal’ way (note that the `JwtService` here is used to create and return a new JWT):

```
import { UsersRepo } from ‘src/dataObjects/users.repo’;
import { JwtService } from ‘@nestjs/jwt’;
. . .
@Injectable()
export class AuthService {
constructor(private usersRepo: UsersRepo, private jwtService: JwtService) {}
```

![Avoiding%20Circular%20Dependency%20Issues%20in%20Nest%20js%20b6d78493748b4dc889db5477131500fd/1cl4PmGjslH8yRyxS4QESdw.jpeg](Avoiding%20Circular%20Dependency%20Issues%20in%20Nest%20js%20b6d78493748b4dc889db5477131500fd/1cl4PmGjslH8yRyxS4QESdw.jpeg)

# Reproducing the Circular Dependency error

If we try to import it the ‘normal’ way (`imports: [UsersModule, …]`) we will get the circular dependency error:

![Avoiding%20Circular%20Dependency%20Issues%20in%20Nest%20js%20b6d78493748b4dc889db5477131500fd/1maFCnwxi4kYr-CPezs2l6Q.jpeg](Avoiding%20Circular%20Dependency%20Issues%20in%20Nest%20js%20b6d78493748b4dc889db5477131500fd/1maFCnwxi4kYr-CPezs2l6Q.jpeg)

The Circular Dependency error

That’s it! Thanks for reading and happy coding!