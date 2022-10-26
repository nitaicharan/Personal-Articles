# How to use the Node.js REPL

Created: September 8, 2022 4:09 PM
Finished: Yes
Finished 📅: September 8, 2022
Order: 38
Source: https://nodejs.dev/en/learn/how-to-use-the-nodejs-repl/
Tags: #nodejs

The `node` command is the one we use to run our Node.js scripts:

```bash
node script.js
```

If we run the `node` command without any script to execute or without any arguments, we start a REPL session:

```bash
node
```

> Note: REPL stands for Read Evaluate Print Loop, and it is a programming language environment (basically a console window) that takes single expression as user input and returns the result back to the console after execution. The REPL session provides a convenient way to quickly test simple JavaScript code.
> 

If you try it now in your terminal, this is what happens:

The command stays in idle mode and waits for us to enter something.

> Tip: if you are unsure how to open your terminal, google "How to open terminal on your-operating-system".
> 

The REPL is waiting for us to enter some JavaScript code, to be more precise.

Start simple and enter

```bash
> console.log('test')
test
undefined
>
```

The first value, `test`, is the output we told the console to print, then we get `undefined` which is the return value of running `console.log()`. Node read this line of code, evaluated it, printed the result, and then went back to waiting for more lines of code. Node will loop through these three steps for every piece of code we execute in the REPL until we exit the session. That is where the REPL got its name.

Node automatically prints the result of any line of JavaScript code without the need to instruct it to do so. For example, type in the following line and press enter:

```bash
> 5 === '5'
false
>
```

Note the difference in the outputs of the above two lines. The Node REPL printed `undefined` after executed `console.log()`, while on the other hand, it just printed the result of `5 === '5'`. You need to keep in mind that the former is just a statement in JavaScript, and the latter is an expression.

In some cases, the code you want to test might need multiple lines. For example, say you want to define a function that generates a random number, in the REPL session type in the following line and press enter:

```bash
function generateRandom() {
...
```

The Node REPL is smart enough to determine that you are not done writing your code yet, and it will go into a multi-line mode for you to type in more code. Now finish your function definition and press enter:

```bash
function generateRandom() {
...return Math.random()
}
undefined
```

Node will get out of the multi-line mode, and print `undefined` since there is no value returned. This multi-line mode is limited. Node offers a more featured editor right inside the REPL. We discuss it below under Dot commands.

The cool thing about the REPL is that it's interactive.

As you write your code, if you press the `tab` key the REPL will try to autocomplete what you wrote to match a variable you already defined or a predefined one.

Try entering the name of a JavaScript class, like `Number`, add a dot and press `tab`.

The REPL will print all the properties and methods you can access on that class:

[Pressing tab reveals object properties](https://nodejs.dev/static/2b60eb9487f93b672da38e391d2e5e56/6937a/tab.png)

![Untitled](How%20to%20use%20the%20Node%20js%20REPL%200c5a4e7a587546fe89b43345fe58905a/Untitled.png)

You can inspect the globals you have access to by typing `global.` and pressing `tab`:

![Untitled](How%20to%20use%20the%20Node%20js%20REPL%200c5a4e7a587546fe89b43345fe58905a/Untitled%201.png)

If after some code you type `_`, that is going to print the result of the last operation.

If you press the `up` arrow key, you will get access to the history of the previous lines of code executed in the current, and even previous REPL sessions.

The REPL has some special commands, all starting with a dot `.`. They are

- `.help`: shows the dot commands help
- `.editor`: enables editor mode, to write multiline JavaScript code with ease. Once you are in this mode, enter ctrl-D to run the code you wrote.
- `.break`: when inputting a multi-line expression, entering the .break command will abort further input. Same as pressing ctrl-C.
- `.clear`: resets the REPL context to an empty object and clears any multi-line expression currently being input.
- `.load`: loads a JavaScript file, relative to the current working directory
- `.save`: saves all you entered in the REPL session to a file (specify the filename)
- `.exit`: exits the repl (same as pressing ctrl-C two times)

The REPL knows when you are typing a multi-line statement without the need to invoke `.editor`.

For example if you start typing an iteration like this:

```bash
[1, 2, 3].forEach(num => {
```

and you press `enter`, the REPL will go to a new line that starts with 3 dots, indicating you can now continue to work on that block.

```bash
... console.log(num)
... })
```

If you type `.break` at the end of a line, the multiline mode will stop and the statement will not be executed.

We can import the REPL in a JavaScript file using `repl`.

```jsx
const repl = require('repl');
```

Using the repl variable we can perform various operations. To start the REPL command prompt, type in the following line

```jsx
repl.start();
```

Run the file in the command line.

```jsx
node repl.js
```

```jsx
> const n = 10
```

You can pass a string which shows when the REPL starts. The default is '> ' (with a trailing space), but we can define custom prompt.

```jsx
// a Unix style prompt
const local = repl.start('$ ');
```

You can display a message while exiting the REPL

```jsx
local.on('exit', () => {
  console.log('exiting repl');
  process.exit();
});
```