# Making frontend filtering expressive

Created: May 30, 2022 8:06 PM
Finished: No
Source: https://medium.com/@sander_hidding/making-frontend-filtering-expressive-618ef69fcbfa

![Making%20frontend%20filtering%20expressive%208e905907595c4355bdcde8e58abe9983/1lcb54loMPrhsVkdnvIpTZA.png](Making%20frontend%20filtering%20expressive%208e905907595c4355bdcde8e58abe9983/1lcb54loMPrhsVkdnvIpTZA.png)

These days the `.map()`, `.filter()` and `.reduce()` methods help out with reducing or manipulating larger datasets. However, imagine that you need all the names of users from the items posted within the last week, that contain over five comments, and sort them by name. This is where Needle comes in! So let’s say you want to retrieve all records that contain an age bigger then 30, wouldn’t it be great to just write `.bigger('age', 30)`? Well now you can. If you need to retrieve chunked result in pairs of 5 from the new array, `.chunk(5)` would do.

Basically Needle helps to find your needle in a haystack of data being only `~5.2kb` gzipped. Filtering tends to get real ugly real soon, also deep nested layers of objects in objects are not easily retrieved and thus it made sense publishing a more intuitive, easier and fun to work with solution. While building Needle, the goal I had in mind was to make frontend filtering a bliss instead of a hassle.

Now why use frontend filtering in the first place? It’s true, not every website calls for a frontend solution when it comes to filtering, however, if your data is limited, frontend filters can be blazing fast — adding to the overall experience and performance of your website. Before setting of creating the package I set a couple of goals:

- ✍️ **Layout options as intuitive as possible:** implementing the syntax as if you are writing a sentence. Needle isn’t a groundbreaking concept, but it’s makes something recurring simple, and that’s what counts in the end!
- **💡 Making code readable and maintainable:** keeping it simple by splitting the responsibilities and therefor preserving the reusability of the codebase. With a simple yet consistent file structure I layed out the foundation of the package.
- **🏗 Writing testable codebase that can be easily decoupled:** making sure that every piece of code is maintainable and preserving separation of concerns. Using both functional programming principles as well as OOP, combining the best of two worlds.
- **✏️ Documenting all the resources and options:** as far as self explaining code goes, documentation makes it easier to start working with an unknown source. By setting some examples and creating an extensive README document to go along with the finished product.

## Simplicity by design

Working with data can be fun, especially if you get to combine it with a smart user experience and smooth transitions. Frontend filtering gives more flexibility to the developer to make something truly outstanding, while maintaining control over the UX without depending to much on fetching backend related endpoints.

You can say whatever you want about jQuery, but you could show the codebase to your mom without having to explain what was going on. Chaining seemed like the way forward with this project, making sure that queries are easy to write and felt more expressive. So let’s dive into a simple examples first demonstrating the actual use.

Simple example of filtering items with Needle

Now, in this case, we have a set of data containing 3 objects with some straight forward data. We can see the query used to make a selection where we select the hobby “Movies” `.where('hobbies', 'Movies')` meaning we first retrieve the first two items from the data array. Ok, great, but now we only need the records with the name “Sander”, so we use the `.andWhere('name', 'Sander')` method to narrow down the selection. “Nicole” and “John” are now excluded. But then we do want to include all items with an age above 40, using the `.orWhere('age', '>', 40)` method we include “John” back into the mix. Now we sort the results using `.sort('name')` and `.log()` the result to the console. We’ve now combined an inclusive and exclusive data filter as well as some sorting with just 5 lines of code, simple, easy and understandable.

![Making%20frontend%20filtering%20expressive%208e905907595c4355bdcde8e58abe9983/1P7LGoa6Jjr7Jwi-kXR46hw.gif](Making%20frontend%20filtering%20expressive%208e905907595c4355bdcde8e58abe9983/1P7LGoa6Jjr7Jwi-kXR46hw.gif)

Using Needle with Vue JS (and Bootstrap) for a simple mockup (GIF doesn’t work well on mobile devices).

## Go the extra mile

Now this all seems cool, but what if you need to do some really extensive filtering, would Needle still hold up? With the `.query()`, `.and()` and `.or()` methods your able to go the extra mile if needed. Either using the methods provided by Needle, while still being able to incorporating custom logic in the mix. In the example below we see the query will present a new parameter for both the dataset, as well as an instance of the package, meaning every option can be either nested, combined and customised as long as you return an array of items.

Advanced example of filtering items with Needle

## Decoupling the logic behind the scenes

It made sense using chained methods to achieve this effect. Chaining methods can only be achieved by reinitiating new instances of the same class. I know, I know, it’s expensive on your memory, so I’ll let you be the judge if you can afford the use of this solution. I’ll assume your not going nuts messing with gigantic data sets and that you are aware of the limits of your browser.

> “So let’s say you want to retrieve all records that contain an age bigger then 30, wouldn’t it be great to just write .bigger('age', 30)? Well now you can!”
> 

Writing object oriented classes in Javascript is still relatively new, and although I like it, I prefer writing my code based on functional programming principles. To avoid creating an enormous class, or extending class upon class upon class, I extended the prototype of the class upon initiation of the script. This ensured that I could create separated files for every piece of logic, while still maintaining a concept of chained methods. A small helper, with a little help from Webpack merges all these functions, leaving only setters and getters within the core module of the package.

The core then extends upon a the Needle class that contains all the methods. Using the magic of `this`, I was able to make sure every method had access to values that needed to be shared. Since Javascript doesn’t support the use of private, protected or public methods I made sure to prefix every method with an `_`. With some preprocessing magic I abstracted both helpers and variables to limit the file size and further abstract (and prevent the use of) these private functions.

With a few exceptions, I was able to keep most functions limited to a small amount of lines. Splitting up the logic within several folders made sure that functions can be easily located and utilities are reusable throughout the codebase. This resulted in a smaller bundle size. I wanted to have a dependency free solution (with the exception of some `devDependencies`) the final build doesn’t rely on any external solutions.

With a few additional tricks the `needle.pkg.js` bundle of the package was limited to only `~5.2kb` gzipped and `~17.4kb` in file size, although it contains, over almost 60 methods intended for use, these methods are responsible for either retrieving, sorting, debugging, templating, filtering, combining, manipulating and/or saving data.

## Testing the code

With small chunks of code and a clear separation of concerns, testing could be easily implemented. For testing I choose to use Jest, the testing framework made by Facebook. Since I already had some experience with Jest, it made perfect sense setting up some automated test to prevent mistakes building the package and making over a 100 files come together during the building process.

Most functions are able to handle different data types for filtering purposes. Meaning either strings, dates, numbers, arrays, and bools can be set as an argument. Take for instance the where method, it is uses a default operator and has multiple uses that handles different outcomes. The example below highlights some of the flexibility of the `where()` method, either searching inside an array, or using an array of values to match multiple values at once. These can be combined as well, meaning search for multiple values (array) within an array of values.

Example of using single method to retrieve multiple outcomes based on data types

With Jest I was able to create test suites that contain multiple uses of the same function, making sure that every intended argument would hold against the final build. Together with Github Actions, I automated my testing on every new push towards the master branche within the project. The ease of use with Github Actions was a true blessing for my efforts to consistently test and prevent breaking changes from making it to production.

## Going under the hood

Although testing gives you some peace of mind, I wanted to be more explicit about the use and logic behind the written code. I have heard a lot about self explaining code and I like the idea, although it can cause some verbose code ending up with `longFunctionsThatFeelLikeEntireSentences`. Documenting code inline prevents developers from having to keep two files open at any given time, also it’s directly available and can give some extra meaningful context about what is actually going on. I do encourage the use of these simple inline comments as they will be stripped from the final build anyway. Reading code is already exhausting enough so let’s make it as pleasurable as possible.

![Making%20frontend%20filtering%20expressive%208e905907595c4355bdcde8e58abe9983/1Vp9LoyXTMmC7dQQGIFtmfA.jpeg](Making%20frontend%20filtering%20expressive%208e905907595c4355bdcde8e58abe9983/1Vp9LoyXTMmC7dQQGIFtmfA.jpeg)

An example of an example visualising the actual use with mockup data

Using the JSDoc markup simple short inline notes give some extra insight into the options for each function within the codebase. Oh yeah, checking out the repository you might have noticed I used Gitmoji to give some “cool” context to my commits as well. You’re welcome to hate. If you don’t want to go under the hood, sure enough there is a `README.md` document containing examples using the different methods into practice. Splitting up the docs in chapters made sure that the file length is limited to the essentials. Even if you don’t like reading (…are you still here?) there is a directory containing examples within a simple `index.html` file that visualises the actual results within tables so you can play around with the use of Needle and some mockup data.

## The experience

After years of exploiting the open source community the least I could do is making a small contribution. There might not be any silver bullets in development, but the distribution of shared knowledge and plug ‘n play solutions has surely boosted up my development process. I can only hope to inspire or help out by giving something back in return. The experience of writing an open source package made me become more aware of the expectations I would have when using a package, and making sure not to make the mistake of developing it from my own point of view, but with a potential user in mind.

Because I worked on the package in my spare time I had some extra time to rethink the decisions made. It made me aware of refactors and using different principles creating something that I would enjoy using myself. Additional insights about managing the bundle size, decoupling, testing, and Github Actions made me gain some new useful insights for any future projects coming up. You can check out [the project on my Github account](https://github.com/waxs/needle) (in progress). Hope you’ll enjoy it! Stay tuned.