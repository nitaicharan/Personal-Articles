# Install an older version of an npm package

Created: August 23, 2022 7:42 AM
Finished: Yes
Finished 📅: August 23, 2022
Order: 18
Source: https://nodejs.dev/en/learn/install-an-older-version-of-an-npm-package
Tags: #nodejs

You can install an old version of an npm package using the `@` syntax:

```bash
npm install <package>@<version>
```

Example:

```bash
npm install cowsay
```

installs version 1.3.1 (at the time of writing).

Install version 1.2.0 with:

```bash
npm install cowsay@1.2.0
```

The same can be done with global packages:

```bash
npm install -g webpack@4.16.4
```

You might also be interested in listing all the previous versions of a package. You can do it with `npm view <package> versions`:

```bash
❯ npm view cowsay versions

[ '1.0.0',
  '1.0.1',
  '1.0.2',
  '1.0.3',
  '1.1.0',
  '1.1.1',
  '1.1.2',
  '1.1.3',
  '1.1.4',
  '1.1.5',
  '1.1.6',
  '1.1.7',
  '1.1.8',
  '1.1.9',
  '1.2.0',
  '1.2.1',
  '1.3.0',
  '1.3.1' ]
```