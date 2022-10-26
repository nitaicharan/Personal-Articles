# How to write Git commit messages like a Pro!

Created: October 13, 2022 5:39 AM
Finished: No
Source: https://dev.to/ritikbanger/how-to-write-git-commit-messages-like-a-pro-dn
Tags: #git

![How%20to%20write%20Git%20commit%20messages%20like%20a%20Pro!%203f42d90d3529480385ff9e0dd2765c01/8cxfqbngfw50n7hcvadb.png](How%20to%20write%20Git%20commit%20messages%20like%20a%20Pro!%203f42d90d3529480385ff9e0dd2765c01/8cxfqbngfw50n7hcvadb.png)

When a developer go back into time to look for something he has worked on six months ago, many times he does not understand why he made that particular commit and the only reason is that he has not followed the right way to write the commit message.

There are commit message standards that devs practice around the world, and it is good to follow popular standards so that when you come back after a good amount of time or someone else looks at your commit messages, it would not look like cringe!

> 
> 
> 
> The most effective technique to inform other developers of the context of a change is with a well-written Git commit message.
> 

Teams should first decide on a commit message convention that specifies the version control history of the product they are building.

A great Git commit message should have a proper style, content, and the metadata.

A known Git commit follows this convention:

`<type>(<scope>): <message>`

`<type>` can be one of the following:

- `feat` for a new feature.
- `refactor` for refactoring production code, e.g. renaming a function.
- `docs` for changes to the documentation.
- `fix` for a bug fix for the user.
- `perf` for performance improvements.
- `style` for formatting changes, missing semicolons, etc.
- `test` for adding missing tests, refactoring tests.
- `build` for updating build configuration, development tools or other changes irrelevant to the user.

You can also add your custom type too, depending on the standards your team follows. The above standards are followed by ESlint team. You can check their commit messages [here](https://github.com/eslint/eslint/commits/).

The scope is optional, and the message part should include a single line statement, not more than 72 characters, to sum up what the commit is for.

Many developers also use the message as the subject line and add a body too, that is basically the description of the commit, but a one-liner commit message is preferable as long as you can tell about the context (commit what's and why's), if the commit demands a more detailed description that can not be explained in a single line, commit body is always necessary.

You can also use tools like [Glitter](https://github.com/Milo123459/glitter) or [Commitizen](http://commitizen.github.io/cz-cli/) to standardize your commit messages.

Not only this, You might also wonder that there is a tool that checks for your commit message and pops an error if it does not follow the guidelines. [Commit lint](https://commitlint.js.org/#/) is one of them. It helps your team adhere to a commit convention.

Many times, industry experts use their JIRA or Click Up ticket as the commit message so that everything can be link or trace back anytime and the codebase remain maintainable for future developers.

Some teams also like to add emoji to their commit messages. I have curated a list of emojis and their respective meanings, you can check it out [here](https://gist.github.com/ritikbanger/649fc5a1694a10dfb274a50ac8d643a4).

At the end, the important thing is that your commit message should be meaningful and does not confuse your fellow developers or the future developers about what a particular change is up to.

If you wish to learn more about conventional commits, semantic commits, or the practices that industry follows, here are some resources for you: