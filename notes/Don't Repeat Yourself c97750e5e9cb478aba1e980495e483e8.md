# Don't Repeat Yourself

Created: April 20, 2022 11:02 PM
Finished: Yes
Finished 📅: April 20, 2022
Source: https://deviq.com/principles/dont-repeat-yourself
Subjects: design pattern
Tags: #design-pattern

The Don't Repeat Yourself (DRY) principle states that duplication in logic should be eliminated via abstraction; duplication in process should be eliminated via automation. ***Duplication is Waste***. Adding additional, unnecessary code to a codebase increases the amount of work required to extend and maintain the software in the future. Duplicate code adds to technical debt. Whether the duplication stems from [Copy Paste Programming](https://deviq.com/antipatterns/copy-paste-programming) or poor understanding of how to apply abstraction, it decreases the quality of the code. Duplication in process is also waste if it can be automated. Manual testing, manual build and integration processes, etc. should all be eliminated whenever possible through the use of automation.

Often, if-then and switch statements have a habit of being duplicated in multiple places within an application. It's common in secured applications to have different functionality available to users in certain roles, so the code may be littered with if-user-is-in-role checks. Other applications may have been extended to deal with several similar but distinct kinds of data structures, with switch() statements at all levels of the workflow used to describe the differences in behavior each data structure should have. Wherever possible, refactor these conditionals using well-known design patterns to abstract the duplication into a single location within the codebase.

[Once and Only Once](https://deviq.com/f7229ba639b55376db740146df156f84/once-and-only-once.md) can be considered a subset of the DRY principle.

The [Open/Closed Principle](https://deviq.com/principles/open-closed-principle) only works when DRY is followed.

The [Single Responsibility Principle](https://deviq.com/principles/single-responsibility-principle) relies on DRY.

[SOLID Principles of Object Oriented Design on Pluralsight](https://www.pluralsight.com/courses/principles-oo-design) (includes 2 modules on DRY)

Don't Repeat Yourself from [97 Things Every Programmer Should Know](http://amzn.to/z5LNUC)

2016 Software Craftsmanship Calendar

[Edit this page on GitHub](https://github.com/ardalis/DevIQ-gatsby/tree/master/src/docs/principles/dont-repeat-yourself.md)

![Don't%20Repeat%20Yourself%20c97750e5e9cb478aba1e980495e483e8/nimblepros-bettersoftwarefaster-white-240x180.png](Don't%20Repeat%20Yourself%20c97750e5e9cb478aba1e980495e483e8/nimblepros-bettersoftwarefaster-white-240x180.png)