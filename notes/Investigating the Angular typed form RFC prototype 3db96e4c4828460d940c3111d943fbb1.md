# Investigating the Angular typed form RFC prototype

Created: August 15, 2022 9:14 AM
Finished: Yes
Finished 📅: August 15, 2022
Source: https://blog.logrocket.com/investigating-angular-typed-form-rfc-prototype/
Tags: #angular

![Investigating%20the%20Angular%20typed%20form%20RFC%20prototype%203db96e4c4828460d940c3111d943fbb1/investigating-angular-typed-form-rfc-prototype.png](Investigating%20the%20Angular%20typed%20form%20RFC%20prototype%203db96e4c4828460d940c3111d943fbb1/investigating-angular-typed-form-rfc-prototype.png)

What is the most popular issue of all time on the Angular repo?

You may already know of [issue #13721](https://github.com/angular/angular/issues/13721). After five years, the long-awaited feature request reached the RFC stage with a [prototype demo](https://stackblitz.com/edit/angular-typed-forms-ex-cvwtas?file=src%2Fapp%2Fprofile%2Fprofile.component.ts).

In this article, I am going to review the history of the issue and examine the RFC and its prototype.

## Why is this the most popular feature request?

Angular has widely been considered the best framework for enterprise apps because of its [forms packages](https://angular.io/api/forms), which are its most important features.

Angular provides two form packages: template-driven forms and reactive forms. Reactive forms are more powerful, testable, and suitable for building more complex forms, such as registration forms with a large number of fields, many validation rules, and a few nested subforms, like addresses and dependents. Reactive forms can handle this complexity more gracefully than template-driven forms.

But once you start working on an Angular reactive form, you’ll quickly notice a problem: it’s not strongly typed. The entire reactive form API doesn’t support strong typing — the properties/methods from `FormControl`, `FormGroup`, and `FormArray` all return `any`. This lack of type safety can cause many potential issues and make it more challenging to maintain complex or nested forms.

As a result, the Angular community has long requested strongly typed reactive forms — since the reactive form API was released. The request is outlined in issue [#13721](https://github.com/angular/angular/issues/13721) in only two lines:

> Reactive forms are meant to be used in complex forms but control’s valueChanges are Observable<any>, which are totally against good practices for complex code.
> 

*There should be a way to create strongly typed form controls.*

Issue #13721 isn’t the only one to request strongly typed forms — there are a number of similar feature requests, like issues [#17000](https://github.com/angular/angular/issues/17000), [#16999](https://github.com/angular/angular/issues/16999), and [#16933](https://github.com/angular/angular/issues/16933) — but none got straight to the point like issue #13721. It is concise and to the point.

Many of us waited for an official solution from the Angular team, but it’s been a long wait. Some developers have tried to work around the lack of type safety on their own, using type definition files or creating custom wrappers. Some have also [created their own third-party libraries](https://blog.logrocket.com/angular-formbuilder-reactive-form-validation/).

All of these attempts have their pros and cons, and none has emerged as the agreed-upon best approach. So, we continued waiting.

## Why did it take so long?

The issue was first opened when the primary stable Angular version was 2.x; Angular 13 was [released in November 2021](https://blog.angular.io/angular-v13-is-now-available-cce66f7bc296). Now that we’ve finally passed the RFC stage, we can say it only took five years and one month to agree on a solution!

To understand why it took so long, I’ll summarize the main events into a timeline below.

- **16 December 2016**: [@asfernandes](https://github.com/asfernandes) opened [issue #13721](https://github.com/angular/angular/issues/13721#issue-198158565)
- **2017–2020**: Many community members and contributors are involved in the discussion, but others attempt to implement their own solutions through various pull requests and some third party libraries:
    - [@KostyaTretyak](https://github.com/KostyaTretyak) and his @ng-stack/forms
    - [@netanelbasal](https://github.com/NetanelBasal) and his @ng-neat/reactive-forms
- **3 June 2020**: [@kara](https://github.com/kara) shared an [update from the Angular team](https://github.com/angular/angular/issues/13721#issuecomment-637698836): “we hear you that this is a big pain point. We will be starting work on more strongly typed forms soon”
- **June–Oct 2020**: After the Angular team confirms they’re working on this issue, a few community members and Angular contributors started more in-depth discussions on different implementation approaches
- **18 November 2020**: [@pauldraper](https://medium.com/u/301b362ad9bc) commented on the #13721 thread that the issue is now marked on the [Angular roadmap](https://angular.io/guide/roadmap#better-developer-ergonomics-with-strict-typing-for-angularforms) for Future development
- **2 February 2021**: [@ristomatti](https://medium.com/u/455ce958d2f3) commented on #13721 thread that the Angular team had studied @ng-stack/forms and worked out a [a draft PR](https://github.com/angular/angular/pull/38406/files)
- **22 September 2021**: [@dylhunn](https://medium.com/u/db1c88da04ac) provided an update to the project status along with his own development [roadmap](https://github.com/angular/angular/issues/13721#issuecomment-924284753), reigniting excitement for the issue and forthcoming prototype
- **17 December 2021**: @dylhunn announced the “Strictly Typed Reactive Forms” [Request for Comment](https://github.com/angular/angular/discussions/44513#discussion-3748538) is now published
- **25 January 2022**: @dylhunn announced that [the Typed Forms RFC is closed](https://github.com/angular/angular/discussions/44513#discussioncomment-2038985)

As you can see, the Angular team only gave a formal acknowledgement of the issue three and half years after it was opened. I don’t want to speculate about why it took so long for the Angular team to respond, but the Angular community showed great persistence and enthusiasm in pursuing this feature request.

## Technical highlights of the strongly typed reactive form

The Angular Typed Forms prototype is implemented by adding generics to `AbstractControl` classes as well as `FormBuilder`. This makes forms type-safe and null-safe for both controls and values.

Initially, @dylhunn tried to use the “value-type” approach, which means the type parameter is the data model/interface.

```
interface Cat {
  name: string;
  lives: number;}const cat = new FormGroup<Cat>({
  name: new FormControl('spot,...),,
  lives:newFormControl(9,...),});
```

But he soon found that this approach was not enough: the value type was not sufficient to [address the deeply-nested FormGroups](https://blog.logrocket.com/reactive-form-controls-form-groups-angular/). Adding the strong type requires not just the `FormGroup.value`, but also `FormGroup.controls`.

### More great articles from LogRocket:

- Don't miss a moment with [The Replay](https://lp.logrocket.com/subscribe-thereplay), a curated newsletter from LogRocket
- [Learn how to animate](https://blog.logrocket.com/animate-react-app-animxyz/) your React app with AnimXYZ
- [Explore Tauri](https://blog.logrocket.com/rust-solid-js-tauri-desktop-app/), a new framework for building binaries
- [Discover](https://blog.logrocket.com/best-typescript-orms/) popular ORMs used in the TypeScript landscape

The final design uses a “control-types” approach. In a nutshell, the type parameter on `FormGroup` is an object containing controls. An example is shown below:

```
const cat = new FormGroup<{
    name: FormControl<string>,
    lives: FormControl<number>,}>(...);
```

### Reset and nullability

The prototype applies strong typing on top of the existing Forms API, and the changes are complex and tricky. A good example is how the control `reset` is being handled. The existing control `reset()` will set the value of control to `null`. This existing behavior causes all the inferred types to become nullable.

```
const dog = new FormControl('spot'); // dog has type FormControl<string|null>
dog.reset();const whichDog = dog.value; // null
```

To overcome this undesired behavior and still keep the existing code unbroken, the `initialValueIsDefault` flag is introduced to allow controls to be `reset()` to their default valu, instead of defaulting to `null`. This approach provides users with the option to avoid unnecessary nullable types, and keeps existing code working at the same time.

```
const dog = new FormControl('spot', {initialValueIsDefault: true}); // dog has type FormControl<string>
dog.reset();const whichDog = dog.value; // spot
```

### How the `Get` method is implemented

Another really cool implementation in the prototype is to use the TypeScript template string literal and recursive type to perform type inference in `AbstractControl.get()`. Here is the code extracted from the PR:

```
// extract from @dylhunn PR #43834 https://github.com/angular/angular/pull/43834/**
 * Tokenize splits a string literal S by a delimeter D.
 */
type Tokenize<S extends string, D extends string> =                                      /*\n*/
    string extends S ? string[] : /* S must be a literal */                              /*\n*/
    S extends `${infer T}${D}${infer U}` ? [T, ...Tokenize<U, D>] : /* Recursive case */ /*\n*/
    [S] /* Base case */                                                                  /*\n*/
    ;/**
 * Navigate takes a type T and an array K, and returns the type of T[K[0]][K[1]][K[2]]...
 */
type Navigate<T, K extends(string|number)[]> =                                 /*\n*/
    T extends object ? /* T must be indexable (object or array) */             /*\n*/
    (K extends [infer K0, ...infer K1] ? /* Split K into head and tail */      /*\n*/
         (K0 extends keyof T ? /* head(K) must index T */                      /*\n*/
              (K1 extends(string|number)[] ? /* tail(K) must be an array */    /*\n*/
                   (Navigate<T[K0], K1>) /* explore T[head(K)] by tail(K) */ : /*\n*/
                   any) /* tail(K) was not an array, give up */ :              /*\n*/
              any) /* head(K) does not index T, give up */ :                   /*\n*/
         T) /* K is empty; just return T */ :                                  /*\n*/
    T /* T is a primitive, return it */                                        /*\n*/
    ;/**
 * Get takes a type T and some property names or indices K.
 * If K is a dot-separated string, it is tokenized into an array before proceeding.
 * Then, the type of the nested property at K is computed: T[K[0]][K[1]][K[2]]...
 * This works with both objects, which are indexed by property name, and arrays, which are indexed
 * numerically.
 *
 * TODO: Array indices work in the format ['foo', 0, 'bar'], but not in the format 'foo.0.bar'. This
 * is not currently possible to support with the TypeScript type system.
 *
 * @publicApi
 */export type Get<T, K> =                                                            /*\n*/
    K extends string ? Get<T, Tokenize<K, '.'>>: /* dot-separated path */          /*\n*/
    K extends Array<string|number>? Navigate<T, K>: /* array of path components */ /*\n*/
    never;
```

The `Get` takes a type `T` and property names as dot-delimited strings or arrays, and returns the type of the sub property type. The example usage is shown below:

```
// Example usage: let's go to a party!

type Party = {
  venue: {
    address: string,
    dates: Array<{
      month: string,
      day: number,
    }>
  },}// This evaluates to `string`.
type whereIsTheParty = Get<Party, 'venue.address'>;// This evaluates to `number`.
type whatDayIsTheParty = Get<Party, ['venue', 'dates', 0, 'day']>;
```

This code block is a perfect use of [TypeScript’s template literal type feature](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html), which became available in TypeScript version 4.1.

## Understanding RFC #44513

In the open source world, a major change to a large framework often comes with a Request for Change (RFC). The RFC is a formal request or proposal for the implementation of a change and contains all information required to approve the change.

[RFC #44513](https://github.com/angular/angular/discussions/44513) is concise and provides the right amount of detail. It includes the following:

- Goals of the RFC
- Introduction of the new API
- Limitations of the API
- Request for feedback on a few important design questions

The RFC makes it very clear that one of the main goals is to maintain backward compatibility and, to quote it, “focus on incrementally adding types to the existing system.” This is similar to TypeScript’s principles for handling JavaScript compatibility: backward compatible, focused on types, and allowing mixed use.

During the RFC discussion, many were concerned about whether their existing code could be migrated smoothly, and some suggested adding extra options for the future Angular update. @dylhunn explained the rationale behind the current strategy and promised to ship the new features with a migration schematic to keep the code that relies on the previous behavior working.

The well-discussed backward compatibility of this design will hopefully make the change easier for developers to adopt in existing code bases.

## Feedback highlights from the RFC period

Needless to say, the Angular community was excited by the RFC. During the four-week RFC period, very active discussions occurred and participants made many constructive suggestions — you can feel the excitement from the conversations.

There were some highlights from the discussions that weren’t included in the final RFC, but I thought were still worth noting:

- **Nullability of `FormControls`**: Questions and suggestions were raised about adding options to handle nullable form controls. Many discussions focused on the new `initialValueIsDefault` flag, which allows `reset()` to set the form controls to a predefined default value
- **Whether current limitations (validators and `ControlValueAccessor`) are a blocker to the release of the feature**: The general consensus was that they’re good to have, but shouldn’t block this much-wanted feature

Many participants have tried out the prototype, and a couple of issues were identified:

- @cexbrayat found the current migration [ignores all lazy-loaded components](https://github.com/angular/angular/discussions/44513#discussioncomment-1836839)
- @kirjs found issues related with the [prototype demo package version](https://github.com/angular/angular/discussions/44513#discussioncomment-1986090)

In his closure statement, @dylhunn summarized the future of the discussion as below:

- “We will not block this feature on template type checking improvements, validators, or CVAs. We may land improvements in these areas, possibly in follow-up releases”
- “We’ve identified some use cases where `FormRecord` (but not `FormTuple`) might be useful and we’ll consider adding it as a followup”
- “Our approach to nullability has tradeoffs, but seems to be an acceptable non-breaking solution”

He also thanked participants for their help and committed to [fixing some implementation issues they uncovered](https://github.com/angular/angular/discussions/44513#discussioncomment-2038985).

## The future of Typed Reactive forms in Angular

In the [RFC closure comments](https://github.com/angular/angular/discussions/44513#discussioncomment-2038985), @dylhunn wrote: “Based on the feedback and the initial prototype, we plan to move forward with the proposed design. We’ll provide more updates as we progress with the implementation and incorporate your feedback, so stay tuned.”

Based on his previous hint, the next milestone will be the formal feature release as part of Angular v14, and it will come with a migration schematic.

The limitations for the current prototype (and the coming first release) is the lack of template type checking for validators and `ControlValueAccessor`. This means the current template type checking won’t be able to catch the type mismatch between the `FormControl` and the template DOM control.

Thus, the next step is to bring these improvements to template type checking.

## Summary

Reactive forms is one of [Angular](https://blog.logrocket.com/tag/angular)’s most popular features, and its lack of strong typing has been a critical issue for a long time. When Angular v14 is released with this feature in the coming months, it will bring reactive forms to the next level — and likely make Angular a more attractive choice for apps that require complex forms.

Thanks to the persistence and hard work of the community, @dylhunn, and other contributors, this request is now a reality! Better late than never. I am looking forward to the upcoming formal release.

## Experience your Angular apps exactly how a user does

Debugging Angular applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking Angular state and actions for all of your users in production, [try LogRocket](https://www2.logrocket.com/angular-performance-monitoring).

[https://logrocket.com/signup/](https://www2.logrocket.com/angular-performance-monitoring)

![Investigating%20the%20Angular%20typed%20form%20RFC%20prototype%203db96e4c4828460d940c3111d943fbb1/610d6a7-687474703a2f2f692e696d6775722e636f6d2f696147547837412e706e67.png](Investigating%20the%20Angular%20typed%20form%20RFC%20prototype%203db96e4c4828460d940c3111d943fbb1/610d6a7-687474703a2f2f692e696d6775722e636f6d2f696147547837412e706e67.png)

[LogRocket](https://www2.logrocket.com/angular-performance-monitoring) is like a DVR for web and mobile apps, recording literally everything that happens on your site including network requests, JavaScript errors, and much more. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred.

The LogRocket NgRx plugin logs Angular state and actions to the LogRocket console, giving you context around what led to an error, and what state the application was in when an issue occurred.

Modernize how you debug your Angular apps - [Start monitoring for free](https://www2.logrocket.com/angular-performance-monitoring).