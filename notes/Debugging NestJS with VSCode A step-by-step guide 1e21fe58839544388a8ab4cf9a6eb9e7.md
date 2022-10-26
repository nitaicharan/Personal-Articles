# Debugging NestJS with VSCode: A step-by-step guide

Created: November 25, 2021 2:05 PM
Finished: Yes
Finished 📅: November 25, 2021
Source: https://javascript.plainenglish.io/debugging-nestjs-in-vscode-d474a088c63b
Tags: #nestjs

[Debugging%20NestJS%20with%20VSCode%20A%20step-by-step%20guide%201e21fe58839544388a8ab4cf9a6eb9e7/0pmiPNItzJ0vjTwKS](Debugging%20NestJS%20with%20VSCode%20A%20step-by-step%20guide%201e21fe58839544388a8ab4cf9a6eb9e7/0pmiPNItzJ0vjTwKS)

Photo by [Olav Ahrens Røtne](https://unsplash.com/@olav_ahrens?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

This article will take a look at three different techniques for debugging a NestJS application with VSCode.

For a quick introduction to NestJS, [check out this article](https://codeburst.io/why-you-should-use-nestjs-for-your-next-project-6a0f6c993be).

Visual Studio Code, or VSCode, is a lightweight source code editor with cross-platform support that boasts an impressive variety of features. Among these features is excellent tooling for debugging applications, including built-in support for Node.js and TypeScript.

While the [NestJS documentation](https://docs.nestjs.com/) is comprehensive, one of the things that it currently does not cover in-depth is debugging.

Fortunately for us, VSCode makes it very straightforward to debug both your NestJS application code and tests written using the [Jest testing framework](https://jestjs.io/docs/en/getting-started). All that’s required is a little bit of tuning.

With that in mind, let’s dive in.

# Approach #1: Launch Configuration

The first approach relies on a `launch.json` file included in a `.vscode/` directory in the root of your workspace.

This JSON file provides configuration information that VSCode uses while figuring out how to debug your application correctly.

Check out [these docs](https://code.visualstudio.com/docs/editor/debugging#_launchjson-attributes) for more on configuring a `launch.json` file.

The JSON file will look like this:

```json
{
  "version": "0.2.0",
  "configurations": [
      {
          "type": "node",
          "request": "launch",
          "name": "Debug Nest Framework",
          "args": [
              "${workspaceFolder}/src/main.ts"
          ],
          "runtimeArgs": [
              "--nolazy",
              "-r",
              "ts-node/register",
              "-r",
              "tsconfig-paths/register"
          ],
          "sourceMaps": true,
          "envFile": "${workspaceFolder}/.env",
          "cwd": "${workspaceRoot}",
          "console": "integratedTerminal",
          "protocol": "inspector"
      }
  ]
}
```

Launch configuration for debugging NestJS

A lot is going on in the above file, so let’s break it down attribute by attribute:

- **type** – Indicates the type of debugger to use. In our case, it’s `node` but if you have a debugging extension for Go you could set it to `go`, etc.
- **request** – The request type of the configuration. This can be either `launch` or `attach` (either launch a new process or attach to an existing one)
- **name** – The name you want to be associated with your debugging configuration
- **args –** Arguments passed to the program that is being debugged. In this case, we provide the path to our `main.ts` file that is the entry point to the application
- **runtimeArgs –** Optional arguments that are passed to the runtime executable. Because we are providing a TypeScript file directly to Node.js, we use `[ts-node](https://www.npmjs.com/package/ts-node)` to run the file without the manual use of the TypeScript compiler
- **sourceMaps –** Use source maps, if found. This is important as it allows us to step through the original TypeScript code, instead of the transpiled JavaScript
- **envFile –** An optional path to a `.env` file. This is necessary if your application relies on environment variables stored in a `.env` file
- **cwd –** The current working directory for finding dependencies and other files
- **console –** Which console to use when displaying debugging output. In this case, we’re indicating we want to use the integrated terminal in VSCode
- **protocol –** Which Node.js debugging runtime protocol to use. The `inspector` protocol is the latest

To use this `launch.json` configuration, first set a breakpoint somewhere in your application’s code.

Then, navigate to the debug pane in the VSCode activity bar by clicking on the icon or by using the keyboard shortcut (`ctrl+shift+D`/`cmd+shift+D`).

Finally, select the `Debug NestJS Framework` configuration from the dropdown and run the debugger by pressing the start icon or using the keyboard shortcut (F5).

At this point, the code should pause at the breakpoint and allow you to debug.

# Approach #2: Nodemon

The second approach to debugging NestJS is to use `[nodemo](https://www.npmjs.com/package/nodemon)n` in conjunction with VSCode’s [auto attach feature](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_auto-attach-feature).

As a quick refresher, `nodemon` is an NPM package that is helpful when developing Node.js applications. It automatically restarts the application when changes to source code files are detected.

To start, we need to make sure we have `nodemon` installed as a development dependency by running `npm i --save-dev nodemon`.

With this in place, we also need an npm script in our `package.json` to run `nodemon`.

Specifically, we add `"start:debug": "nodemon --config nodemon-debug.json"`.

We’ll also need the `nodemon-debug.json` file referenced in the above script as shown below:

```json
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["src/**/*.spec.ts"],
  "exec": "node --inspect-brk -r tsconfig-paths/register -r ts-node/register src/main.ts "
}
```

Nodemon debugging configuration

The above configuration file tells `nodemon` to do the following:

- Watch the code in the `src/` directory for changes
- Look for files with a `.ts` extension
- Ignore any files in the `src/` directory that end with `.spec.ts` (our test files)
- Execute a command. In the above, we use the Node.js runtime as well as `[ts-node](https://www.npmjs.com/package/ts-node)` to run our application in debug mode

The final step is to enable VSCode’s auto attach feature. This feature will automatically connect the VSCode debugger to any Node.js process that has been launched in the integrated terminal.

This feature can be turned on using the [command palette](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) (`ctrl+shift+P`/`cmd+shift+P`) and selecting `Toggle Auto Attach`.

You can now set a breakpoint, run `npm run start:debug` in the integrated terminal, and debug your application.

# Approach #3: Launch Configuration for Jest

This final approach will cover debugging Jest tests in your NestJS application using a new configuration in `launch.json`.

The configuration we need to append to the `launch.json` file for debugging Jest is shown below:

```json
{
  "name": "Debug Jest Tests",
  "type": "node",
  "request": "launch",
  "runtimeArgs": [
    "--inspect-brk",
    "${workspaceRoot}/node_modules/.bin/jest",
    "--runInBand",
    "--coverage",
    "false"
  ],
  "console": "integratedTerminal"
}
```

Launch configuration for debugging Jest tests

This configuration is similar to the first configuration we used for debugging NestJS application code, though this time it is passing the path to the Jest executable in `node_modules/.bin/` to the Node.js runtime.

We are also instructing Jest to run tests serially (instead of the default of concurrently) given the `--runInBand` flag in the `runtimeArgs` array. We opt not to collect test coverage while debugging given `--coverage` is set to false.

With this configuration set, we can set a breakpoint anywhere in our test files and run the VSCode debugger (assuming you’ve selected the new `Debug Jest Tests` configuration from the dropdown in the debugging pane of the activity bar).

# Closing Thoughts

While a quick `console.log` statement is often useful for quick debugging tasks, it doesn’t scale well when debugging large, more complex issues. This situation is when a robust debugging setup comes in handy.

Debugging NestJS can be a challenge given the framework’s use of TypeScript and dependency injection. Fortunately for us, VSCode’s debugging tooling makes this a relatively painless experience.

Hopefully, this article was informative and simplifies debugging on your next NestJS project.

As always, I’d love to hear any thoughts and feedback about NestJS/VSCode/debugging. Until next time, thanks for reading.

# **References**

- [Source code for this article](https://github.com/nmchenry01/nestjs-blogs/tree/debugging-nestjs)
- [Introduction to NestJS](https://codeburst.io/why-you-should-use-nestjs-for-your-next-project-6a0f6c993be)
- [NestJS documentation](https://docs.nestjs.com/)
- [VSCode documentation](https://code.visualstudio.com/docs)
- [Jest documentation](https://jestjs.io/docs/en/getting-started)