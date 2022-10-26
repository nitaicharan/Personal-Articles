# Angular DevTools Released, Includes Component Explorer and Profiler

Created: August 23, 2022 7:35 AM
Finished: No
Source: https://www.infoq.com/news/2021/05/angular-devtools-profiler/
Tags: #angular

[Minko Gechev](https://www.linkedin.com/in/mgechev), software engineer working on Angular at Google, recently [announced the Angular DevTools Chrome extension](https://blog.angular.io/introducing-angular-devtools-2d59ff4cf62f). Angular DevTools includes a component explorer and profiler that let developers visualize component trees and analyze change detection cycles. Angular DevTools supports applications built with Angular v9 and above with Ivy enabled.

Gechev summarized the motivation behind the Angular DevTools as follows:

> 
> 
> 
> Based on the results we received from external and internal studies we identified the following areas which need the most attention:
> 
> - Improvements in error messages
> - Understanding change detection execution
> - Understanding injector hierarchy and provider instantiation
> - Visualization of component structure […] To address [the second and fourth concerns] and provide an Angular-specific view based on the Chrome DevTools features, we developed Angular DevTools.

Angular DevTools includes a component explorer where developers can preview the component tree of an Angular application:

![Angular%20DevTools%20Released,%20Includes%20Component%20Expl%20a1c2fb3d1b564c999cd00248f79d3f60/R7rNJQx.png](Angular%20DevTools%20Released,%20Includes%20Component%20Expl%20a1c2fb3d1b564c999cd00248f79d3f60/R7rNJQx.png)

The component explorer displays components’ metadata, properties, inputs and outputs in the right panel. Developers can modify properties directly in the developer tool user interface, and see the changes immediately reflected in the Angular application.

Angular DevTools also includes a profiler that developers may use to identify possible performance bottlenecks. The profiler lets developers preview change detection cycles as they occur in real time:

![Angular%20DevTools%20Released,%20Includes%20Component%20Expl%20a1c2fb3d1b564c999cd00248f79d3f60/uk5Z6aw.png](Angular%20DevTools%20Released,%20Includes%20Component%20Expl%20a1c2fb3d1b564c999cd00248f79d3f60/uk5Z6aw.png)

Each bar in the timeline corresponds to a separate change detection cycle. By selecting a bar, developers can see what triggered change detection, how much time Angular spends in the change detection phase, and whether the current cycle costs any frame drops. The profiler additionally includes a flame graph and treemap visualizations to better understand the execution of change detection cycles. An example of treemap visualization is as follows:

![Angular%20DevTools%20Released,%20Includes%20Component%20Expl%20a1c2fb3d1b564c999cd00248f79d3f60/e9IiSqT.png](Angular%20DevTools%20Released,%20Includes%20Component%20Expl%20a1c2fb3d1b564c999cd00248f79d3f60/e9IiSqT.png)

While Angular developers [could already profile Angular applications in the regular Chrome DevTools](https://www.youtube.com/watch?v=FjyX_hkscII), Angular DevTools arguably provide an Angular-centric experience, with possible productivity gains when debugging and profiling applications.

Developers will find [all necessary instructions to install the Angular DevTools](https://www.youtube.com/watch?v=bavWOHZM6zE) and a quick demonstration in a separate video available online.

Angular is open source software distributed under the MIT license. Contributions are welcome via the [Angular GitHub repository](https://github.com/angular/angular).

## [Inspired by this content? Write for InfoQ.](https://www.infoq.com/url/t/9f7cc765-74cd-4913-a027-18f5d861b56d/?label=Long-Text-Ad)

[Becoming an editor for InfoQ was one of the **best decisions of my career**. It has challenged me and **helped me grow in so many ways**. We'd love to have more people **join our team**.](https://www.infoq.com/url/t/9f7cc765-74cd-4913-a027-18f5d861b56d/?label=Long-Text-Ad)