# Expressive error handling in TypeScript and benefits for domain-driven design

Created: September 27, 2022 8:20 PM
Finished: No
Source: https://medium.com/inato/expressive-error-handling-in-typescript-and-benefits-for-domain-driven-design-70726e061c86

[Expressive%20error%20handling%20in%20TypeScript%20and%20benefi%20ccd786360f4a46e1aac6d161c5e27813/0iHzzRgUFCulhv0kU](Expressive%20error%20handling%20in%20TypeScript%20and%20benefi%20ccd786360f4a46e1aac6d161c5e27813/0iHzzRgUFCulhv0kU)

Photo by [Fahrul Azmi](https://unsplash.com/@fahrulazmi?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# What error handling looks like when you start a project

Correctly handling and throwing errors in Javascript, or Typescript in our case, is always a painful experience and a journey with many places to get it wrong.

In the early times of a project, error handling is often under-estimated if not absent : some methods throw errors when they really have no choice but to, other errors are thrown out of our codebase because some supposedly impossible states occurred and ultimately **all errors bubble up to the entry point of the application.**

It quickly becomes unmanageable as the project matures, when you want to handle errors differently depending on their origins, causes or types.

Moreover, throwing an error is stopping everything so, as you progressively craft complex use-cases with several actions, you often end up needing a safe way to do actions that might possibly fail and continue even if they fail.

So, what is wrong with most errors thrown/catched ?

## Throwing is silent, from a method’s signature point of view

If we take a function, computing the sum of several patient counts to derive a global patient count :

This version is exactly identical, in terms of signature, to

From the caller perspective, it’s impossible to derive from the method’s signature, obviously without looking at implementation details, that this method might throw an Error.

It makes the method’s signature **incomplete** and potentially dangerously uninformative.

## Throwing error invites the caller not to care about it

Since throwing error does not reflect on the method’s signature, all the safety of typed languages such as Typescript becomes useless since you are neither forced nor hinted, as a caller of such method, to do something with that error.

This characteristic tends to de-responsibilize developers about erroneous states in the application/project and lead to high number of errors un-handled up to entry point of the project.

Chances are that the first time this method was written was coincidental with the first time the method was called, so the developer obviously knew that this method was possibly throwing. Fast-forward six months later, a new developer joins the team and has to re-use this function or a function calling that function, in the case of a bigger task. Throwing error raises the risk of this new developer failing to see that one of the method called might throw, and all the typing safety of Typescript won’t help.

# What one can do about it

## Return a different result instead of throwing

If we take back our previous example :

We might want to affect the method signature to force the caller to handle that `At least one patient count was negative` case according to its context.

One common pattern is to change the function to:

The great advantage is that the method’s signature is affected and any caller is forced to deal with it, like the following example :

**The responsibility of handling has been moved from that error up to the caller**, which is both **more secure**, as this error could be handled differently depending on the context of the caller, and **more expressive** as the caller has not choice but to handle it.

However, this approach quickly turns out to be limited, as the expressivity of the method’s signature is still pretty poor. `null` has no specific semantic meaning, and is used interchangeably in the entire codebase. Besides, returning `null` (or `undefined` or whatever placeholder) makes it impossible to support several error types in a given method since they will all eventually be returned as `null` and the origin of that `null` cannot be traced back.

Finally, another significative shortcoming of this approach is that it breaks the linear flow of a method : You are forced, as a caller, to branch out with a `if(myResult == null)` for each call to such method, which proves to decrease the readability and simplicity of a large use-case, calling multiple methods.

## So, what then?

If we summarise the current findings :

- We want our method’s signature to explicitly express the possibility of erroneous states
- We would like to support an arbitrary number of error types per method
- We don’t want to break the flow of the caller of this method if it’s not necessary

It turns out we can leverage Typescript and its [generics](https://www.typescriptlang.org/docs/handbook/generics.html) to build a structure to do just that: `Either`! This pattern is highly inspired from the venerable `fp-ts` [library](https://github.com/gcanti/fp-ts/)

Let’s look at the following code :

We take advantage from genericity and type inference to create two classes `Left` and `Right` that we will use in an union type `Either`. They both share the same interface but have different behaviours depending on which side of the `Either` is used.

## Using `Either` in our example

Let’s change our example to see how we could see our new structure.

The function returns **either** an error object with a message, or the result as a number. Then the caller could become something like that :

We are happy since we have checked our first two requirements (signature expressivity, and support of as many errors as we want). However, as of right now, the caller’s flow is as fragmented as previous situations.

## Super-charging `Either`

One of the most impactful benefit of `Either` is that you can add methods to both `Left` and `Right` classes to improve the use of it.

Let’s try to improve the caller flow extracted from the example above.

A common pattern is to **get the result of a function : if that is an error, then forward it and otherwise do something with the result**.

In `Either` dialect, it means that we want to apply a function **only on the right branch**.

Let’s add this ! :

We have introduced the same method, `applyOnRight` in the two classes `Left` and `Right` but the implementation drastically differs between the two :

- If the object is an instance of `Left` it does nothing and returns itself as an `Either<L,B>` object.
- If the object is an instance of `Right` it applies the function on its own `value` and also returns an `Either<L,B>` object

The great benefit of this function is that we can refactor the caller example to :

if `computeSomething` also returns a number, then the final signature of the caller would be `Either<{message: string;}, number>` too.

> Returning an Either forces the caller to do something with it, but he has an efficient and clean way to only deal with one of the branch and forward the other branch, therefore impacting its own signature.
> 

You should also note that `applyOnRight` can be chained one after the other, creating a chain of operations applied on the right (e.g. often `success`) branch with the final signature being faithful to the possibility of errors.

We have checked the third requirement: you are not forced to break your flow with multiple `if` statements if you only want to do things on the `success path`

# `Either` at the rescue of error handling in Domain-Driven Design

This section will assume you are familiar with the most fundamental concepts of Domain-Driven Design (DDD).

Error handling is even more crucial when you are following DDD principles, because some **errors can and should be part of your business knowledge.**

## Which errors are relevant for your core domain ?

In general, and even more in the context of DDD, it’s interesting to put errors in one of the two following buckets :

1. **Expected error:** Sometimes, you can have known erroneous states that have logical origins and that the caller can understand and handle usefully. For instance, in our `sumPatientCounts` example, the error raised when at least one of the patient count is negative **is deterministic and meaningful** and therefore is **an expected error.** Each time I will call this function with one negative patient count, I will get the error and reading the error message makes me understand an enforced business rule in the domain. These are the most important errors in DDD, as they are meaningful and convey business logic.
2. **Unexpected error:** On the other hand, some errors are unexpected and do not convey business logic. These errors are very common as soon as you rely on indeterministic functions, like fetching an object from a database, storing a file, calling an external API. The fact that I tried to fetch an object from a database and that, because of network issues, the database cannot respond, is not deterministic, unexpected is not part of my core domain (unless your core domain IS about dealing with databases of course).

> The expected errors are the ones you really want to avoid throwing and that should appear in the method’s signature.
> 

Otherwise, the business rule enforced by this erroneous state becomes implicit and you **have failed to express it in your core domain.**

## Expected errors are gifts for your domain

**Context :** Let’s say your domain have an `User` ValueObject and you would like to enforce that no User can be created with empty email, first name or last name. This is a business rule and you need to make it explicit to other parts of your domain using this value object.

Most of expected errors in your domain will have a type and possibly a message explaining the reason of that failure. Let’s introduce such simple structure doing that :

Let’s go back to our simple Either structure and try to use it to make a business rule conveyed by an erroneous state explicit.

You can see that in this case, the error thrown by the `build` method is explicitly integrated in your core domain. The method's signature directly hints at the business rule enforced.

Regarding the code, we have introduced a generic simple structure `Failure` which can be used and become the standard way of returning error in the domain

The file `error.ts` shows that you can and probably should group the expected errors by a common scope, most frequently around the Entity/ValueObject (here `User`), domain Service or applicative use-case raising them so that the errors' types are even more expressive.

You will often end up with almost self-explanatory error types like `UserCreationError.NotAuthorized` or `TransferFundError.NotEnoughFund`

## Transaction integrity

In the context of DDD, and even in general software engineering, you often need to either execute several instructions successfully or execute none of them if at least one fails.

Examples in domain-driven design could be:

- *Storing an aggregate*: When storing an aggregate, you want to make sure you have successfully persisted the Entities living inside that aggregate but you don’t want to store any of them if one failure occurred, so that you don’t mess with the business coherence enforced by the aggregate.
- *Domain service*: Let’s say you have a domain service transferring funds between an account and another account. You absolutely want to either credit the second account AND debit the first account or do nothing.

If one of the methods called in the transaction throw an error and that you forget to surround it with a `try/catch`, you take the risk that some of the instructions have been executed and that the rest of the instructions will never be, therefore breaking the integrity.

When you need a transaction-like mechanism, it helps a lot to have all the functions called in this transaction explicitly return their possible errors. Typescript will force the developer to handle each erroneous state, making it easier to check that every instructions is executed or none.

Using Either as the default way to return your expected errors won’t help you implement the actual transaction mechanism (e.g rollback + commit) but will drastically lower the odds that one misses a function possibly throwing errors in the transaction body.

For instance in the example of the domain service in charge of transferring funds :

**Naive version :**

*Remark:* We have omitted the transaction mechanism here on purpose, for clarity.

You can see that we want to store the aggregates `debtor` and `creditor` (and send notifications) **only** if we successfully took money from the debtor and gave it to the creditor.

Having the function `takeMoney` and `giveMoney` return results like`Either<CustomerError.InsufficientFund, 'Success'>` or `Either<CustomerError.AccountAlreadyFull, 'Success'>` would :

- Let you easily check that both operations are successful and **which action failed** in case of errors.
- Keep intact the semantic of each action’s result, therefore making it straightforward to take decisions depending on what failed - you don’t want to handle a creditor’s account being full and a debtor’s account being empty the same way.

Those two features would a bit messy to implement without `Either` as you would need to to surround **each action** with a `try/catch`

# Conclusion

The takeaway of this article is definitely not to handle all errors explicitly with a `Either`, some errors are and will remain unexpected and should throw and not pollute signatures and code, but more **that expected and meaningful errors should be expressed both in method's signature and in the caller's code.**

Your methods will gain expressivity and safety and you or your team, as developers, will gain responsibility and ownership regarding error handling, preventing numerous errors bubbling up to the top in production.

*Drug discovery is a challenging, intellectually complex, and rewarding endeavour: we help develop effective and safe cures to diseases affecting millions of people. If you’re looking to have a massive impact, join us! [https://angel.co/inato/jobs/](https://angel.co/inato/jobs)*