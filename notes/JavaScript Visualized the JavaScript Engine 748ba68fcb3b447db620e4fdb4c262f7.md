# JavaScript Visualized: the JavaScript Engine

Created: August 11, 2022 11:26 PM
Finished: No
Source: https://dev.to/lydiahallie/javascript-visualized-the-javascript-engine-4cdf

![JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/q0vxo5pcm6qjo14k0ami.png](JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/q0vxo5pcm6qjo14k0ami.png)

[1](https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif)    [2](https://dev.to/lydiahallie/javascript-visualized-hoisting-478h)    [...](https://dev.to/lydiahallie/javascript-visualized-scope-chain-13pd)       [6](https://dev.to/lydiahallie/javascript-visualized-generators-and-iterators-e36)    [7](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke)

JavaScript is cool (don't @ me), but how can a machine actually understand the code you've written? As JavaScript devs, we usually don't have to deal with compilers ourselves. However, it's definitely good to know *the basics* of the JavaScript engine and see how it handles our human-friendly JS code, and turns it into something machines understand! 🥳

| **Note:** This post is mainly based on the V8 engine used by Node.js and Chromium-based browsers.

The HTML parser encounters a `script` tag with a source. Code from this source gets loaded from either the **network**, **cache**, or an installed **service worker**. The response is the requested script as a **stream of bytes**, which the byte stream decoder takes care of! The **byte stream decoder** decodes the stream of bytes as it’s being downloaded.

![JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/pv4y4w0doztvmp8ei0ki.gif](JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/pv4y4w0doztvmp8ei0ki.gif)

The byte stream decoder creates **tokens** from the decoded stream of bytes. For example, `0066` decodes to `f`, `0075` to `u`, `006e` to `n`, `0063` to `c`, `0074` to `t`, `0069` to `i`, `006f` to `o`, and `006e` to `n` followed by a white space. Seems like you wrote `function`! This is a reserved keyword in JavaScript, a token gets created, and sent to the parser (and *pre-parser*, which I didn't cover in the gifs but will explain later). The same happens for the rest of the byte stream.

![JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/bic727jhzu0i8uep8v0k.gif](JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/bic727jhzu0i8uep8v0k.gif)

The engine uses two parsers: the **pre-parser**, and the **parser**. In order to reduce the time it takes to load up a website, the engine tries to avoid parsing code that's not necessary right away. The preparser handles code that may be used later on, while the parser handles the code that’s needed immediately! If a certain function will only get invoked after a user clicks a button, it's not necessary that this code is compiled immediately just to load up a website. If the user eventually ends up clicking the button and requiring that piece of code, it gets sent to the parser.

The parser creates nodes based on the tokens it receives from the byte stream decoder. With these nodes, it creates an Abstract Syntax Tree, or AST. 🌳

![JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/sgr7ih6t7zm2ek28rtg6.gif](JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/sgr7ih6t7zm2ek28rtg6.gif)

Next, it's time for the **interpreter**! The interpreter which walks through the AST, and generates **byte code** based on the information that the AST contains. Once the byte code has been generated fully, the AST is deleted, clearing up memory space. Finally, we have something that a machine can work with! 🎉

![JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/i5f0vmcjnkhireehicyn.gif](JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/i5f0vmcjnkhireehicyn.gif)

Although byte code is fast, it can be faster. As this bytecode runs, information is being generated. It can detect whether certain behavior happens often, and the types of the data that’s been used. Maybe you've been invoking a function dozens of times: it's time to optimize this so it'll run even faster! 🏃🏽‍♀️

The byte code, together with the generated type feedback, is sent to an **optimizing compiler**. The optimizing compiler takes the byte code and type feedback, and generates highly optimized machine code from these. 🚀

![JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/ongt4qftovd82sp2vihk.gif](JavaScript%20Visualized%20the%20JavaScript%20Engine%20748ba68fcb3b447db620e4fdb4c262f7/ongt4qftovd82sp2vihk.gif)

JavaScript is a dynamically typed language, meaning that the types of data can change constantly. It would be extremely slow if the JavaScript engine had to check each time which data type a certain value has.

In order to reduce the time it takes to interpret the code, optimized machine code only handles the cases the engine has seen before while running the bytecode. If we repeatedly used a certain piece of code that returned the *same* data type over and over, the optimized machine code can simply be re-used in order to speed things up. However, since JavaScript is dynamically typed, it can happen that the same piece of code suddenly returns a different type of data. If that happens, the machine code gets de-optimized, and the engine falls back to interpreting the generated byte code.

Say a certain function is invoked a 100 times and has always returned the same value so far. It will *assume* that it will also return this value the 101st time you invoke it.

Let’s say that we have the following function sum, that’s (so far) always been called with numerical values as arguments each time:

This returns the number `3`! The next time we invoke it, it will assume that we’re invoking it again with two numerical values.

If that’s true, no dynamic lookup is required, and it can just re-use the optimized machine code. Else, if the assumption was incorrect, it will revert back to the original byte code instead of the optimized machine code.

For example, the next time we invoke it, we pass a string instead of a number. Since JavaScript is dynamically typed, we can do this without any errors!

This means that the number `2` will get coerced into a string, and the function will return the string `"12"` instead. It goes back to executing the interpreted bytecode and updates the type feedback.

I hope this post was useful to you! 😊 Of course, there are many parts to the engine that I haven't covered in this post (JS heap, call stack, etc.) which I might cover later! I definitely encourage you to start to doing some research yourself if you're interested in the internals of JavaScript, V8 is open source and has some great documentation on how it works under the hood! 🤖

[V8 Docs](https://v8.dev/) || [V8 Github](https://github.com/v8/v8) || [Chrome University 2018: Life Of A Script](https://www.youtube.com/watch?v=voDhHPNMEzg&t=729s%3Cbr%3E%0A)

FAQ: I use Keynote to make the animations and screen record it lol. Feel free to translate this blog to your language, and thanks so much for doing so! Just keep a reference to the original article and let me know if you've translated it please! 😊

[1](https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif)    [2](https://dev.to/lydiahallie/javascript-visualized-hoisting-478h)    [...](https://dev.to/lydiahallie/javascript-visualized-scope-chain-13pd)       [6](https://dev.to/lydiahallie/javascript-visualized-generators-and-iterators-e36)    [7](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke)