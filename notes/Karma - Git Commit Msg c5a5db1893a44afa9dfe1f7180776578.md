# Karma - Git Commit Msg

Created: October 2, 2022 12:17 AM
Finished: No
Source: http://karma-runner.github.io/1.0/dev/git-commit-msg.html
Tags: #git

## The reasons for these conventions:

- automatic generating of the changelog
- simple navigation through git history (e.g. ignoring style changes)

## Format of the commit message:

```
<type>(<scope>): <subject>

<body>

<footer>

```

## Example commit message:

```
fix(middleware): ensure Range headers adhere more closely to RFC 2616

Add one new dependency, use `range-parser` (Express dependency) to compute
range. It is more well-tested in the wild.

Fixes #2310

```

## Message subject (first line)

The first line cannot be longer than 70 characters, the second line is always blank and other lines should be wrapped at 80 characters. The type and scope should always be lowercase as shown below.

### Allowed `<type>` values:

- **feat** (new feature for the user, not a new feature for build script)
- **fix** (bug fix for the user, not a fix to a build script)
- **docs** (changes to the documentation)
- **style** (formatting, missing semi colons, etc; no production code change)
- **refactor** (refactoring production code, eg. renaming a variable)
- **test** (adding missing tests, refactoring tests; no production code change)
- **chore** (updating grunt tasks etc; no production code change)

### Example `<scope>` values:

- init
- runner
- watcher
- config
- web-server
- proxy
- etc.

The `<scope>` can be empty (e.g. if the change is a global or difficult to assign to a single component), in which case the parentheses are omitted. In smaller projects such as Karma plugins, the `<scope>` is empty.

## Message body

- uses the imperative, present tense: “change” not “changed” nor “changes”
- includes motivation for the change and contrasts with previous behavior

For more info about message body, see:

## Message footer

### Referencing issues

Closed issues should be listed on a separate line in the footer prefixed with "Closes" keyword like this:

or in the case of multiple issues:

```
Closes #123, #245, #992

```

### Breaking changes

All breaking changes have to be mentioned in footer with the description of the change, justification and migration notes.

```
BREAKING CHANGE:

`port-runner` command line option has changed to `runner-port`, so that it is
consistent with the configuration file syntax.

To migrate your project, change all the commands, where you use `--port-runner`
to `--runner-port`.

```

This document is based on [AngularJS Git Commit Msg Convention](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#). See the [commit history](https://github.com/karma-runner/karma/commits/master) for examples of properly-formatted commit messages.