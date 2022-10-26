# React Crash Course for Angular Developers: React Architecture

Created: September 12, 2022 11:23 AM
Finished: No
Source: https://javascript.plainenglish.io/react-crash-course-for-angular-developers-react-architecture-3353e270dc09
Tags: #angular, #react

![React%20Crash%20Course%20for%20Angular%20Developers%20React%20Ar%202354b2425cf24124adf45ce22d7f0c95/1GDHhsQxGqnSfhPgpsR53zw.png](React%20Crash%20Course%20for%20Angular%20Developers%20React%20Ar%202354b2425cf24124adf45ce22d7f0c95/1GDHhsQxGqnSfhPgpsR53zw.png)

Angular and React, by [simform](https://www.simform.com/blog/angular-vs-react/)

I recently talked with a colleague who is an Angular specialist but was looking to learn React to add one more tool to his portfolio. We were talking about similar ways to do the same thing in Angular and React and the idea for this article came about: **how about a React Crash Course for Angular Developers?** In the end, it is frontend or UI development, and most of the knowledge is transferable.

We will divide the course into topics and how things are done in Angular and then the React counterpart side by side.

This is an intensive-turbo-crash course, which means that I’ll be assuming you are familiar with a lot of Web Development concepts but if not, I’ll be adding reference links at the end of each chapter, let’s begin.

# React Architecture.

> A JavaScript library for building user interfaces (official site).
> 

![React%20Crash%20Course%20for%20Angular%20Developers%20React%20Ar%202354b2425cf24124adf45ce22d7f0c95/1rupPWcxOMsC_mFaRl1bk6A.jpeg](React%20Crash%20Course%20for%20Angular%20Developers%20React%20Ar%202354b2425cf24124adf45ce22d7f0c95/1rupPWcxOMsC_mFaRl1bk6A.jpeg)

Real vs virtual DOM, by [altexsoft](https://www.altexsoft.com/blog/engineering/the-good-and-the-bad-of-reactjs-and-react-native/)

One of the main differences between React and Angular is that with the latter you get a lot of out-the-box: a Router, HTTP request client, Pipes, Directives, etc.

But React main responsibility is to allow you to build and render your IU components, and it does that using a [data structure](https://www.geeksforgeeks.org/data-structures/#:~:text=A%20data%20structure%20is%20a,%2C%20retrieving%2C%20and%20storing%20data.) called virtual DOM and reconciliation algorithm.

# How does React work?

## Virtual DOM and the reconciliation algorithm

Virtual DOM is a data structure built to mimic the real DOM with one difference:

> A virtual DOM object has the same properties as a real DOM object, but it lacks the real thing’s power to directly change what’s on the screen, codecademy.
> 

The thing is that operations to update the real [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) are generally expensive in memory.

When you update the component [state](https://en.wikipedia.org/wiki/State_(computer_science)) in React these changes are applied to the virtual DOM, then it’s time for the reconciliation algorithm (or diffing algorithm) to compare the changes between the virtual DOM and the Real DOM.

**Finally, it will apply and update the changes only to those objects and only those objects in the Real DOM.**

**if you’re familiar with Git, you could think of DOM as your main branch, Virtual DOM as your Develop branch, and the reconciliation algorithm as your Pull Request,** it’s not exactly the same but it’s a mental model that makes it easier to understand the concept.

![React%20Crash%20Course%20for%20Angular%20Developers%20React%20Ar%202354b2425cf24124adf45ce22d7f0c95/1gNQmOzE_8SKbTecqmPAPuw.jpeg](React%20Crash%20Course%20for%20Angular%20Developers%20React%20Ar%202354b2425cf24124adf45ce22d7f0c95/1gNQmOzE_8SKbTecqmPAPuw.jpeg)

Virtual DOM and the reconciliation algorithm, by [brainhub](https://brainhub.eu/library/what-is-react)

> React can update only the necessary parts of the DOM. React’s reputation for performance comes largely from this innovation, Codecademy.
> 

Also, this abstracts out the attribute manipulation, event handling, and manual DOM updating that you would otherwise have to use to build your app according.

# Next Chapter: Components in React vs Angular (Next week)

For now, this is enough of React’s architecture, later in the course, we’re going to revisit it expanding it with more stuff like React Fiber and other interesting things.

In the next chapter, we’re going to check the side-by-side differences between React and Angular components: the basics, local state management, component life cycles, and basic styling.

**If would like to know when the next chapter in this course gets released, please subscribe to my newsletter at [davidjsmoreno.dev](https://davidjsmoreno.dev/).**

# Learn more