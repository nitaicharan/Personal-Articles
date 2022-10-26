# Update all the Node.js dependencies to their latest version

Created: August 23, 2022 7:45 AM
Finished: Yes
Finished 📅: August 23, 2022
Order: 19
Source: https://nodejs.dev/en/learn/update-all-the-nodejs-dependencies-to-their-latest-version
Tags: #nodejs

When you install a package using `npm install <packagename>`, the latest version is downloaded to the `node_modules` folder. A corresponding entry is added to `package.json` and `package-lock.json` in the current folder.

npm determines the dependencies and installs their latest versions as well.

Let's say you install `[cowsay](https://www.npmjs.com/package/cowsay)`, a nifty command-line tool that lets you make a cow say *things*.

When you run `npm install cowsay`, this entry is added to the `package.json` file:

```bash
{
  "dependencies": {
    "cowsay": "^1.3.1"
  }
}
```

This is an extract of `package-lock.json` (nested dependencies were removed for clarity):

```json
{
  "requires": true,
  "lockfileVersion": 1,
  "dependencies": {
    "cowsay": {
      "version": "1.3.1",
      "resolved": "https://registry.npmjs.org/cowsay/-/cowsay-1.3.1.tgz",
      "integrity": "sha512-3PVFe6FePVtPj1HTeLin9v8WyLl+VmM1l1H/5P+BTTDkMAjufp+0F9eLjzRnOHzVAYeIYFF5po5NjRrgefnRMQ==",
      "requires": {
        "get-stdin": "^5.0.1",
        "optimist": "~0.6.1",
        "string-width": "~2.1.1",
        "strip-eof": "^1.0.0"
      }
    }
  }
}
```

Now those 2 files tell us that we installed version `1.3.1` of cowsay, and our [npm versioning rule](https://docs.npmjs.com/about-semantic-versioning) for updates is `^1.3.1`. This means npm can update to patch and minor releases: `1.3.2`, `1.4.0` and so on.

If there is a new minor or patch release and we type `npm update`, the installed version is updated, and the `package-lock.json` file diligently filled with the new version.

Since npm version 5.0.0, `npm update` updates `package.json` with newer minor or patch versions. Use `npm update --no-save` to prevent modifying `package.json`.

To discover new package releases, use `npm outdated`.

Here's the list of a few outdated packages in a repository:

[outdated packages](https://nodejs.dev/static/e967736f8d105e64c22d80ce7a42f52a/ee455/outdated-packages.png)

![Untitled](Update%20all%20the%20Node%20js%20dependencies%20to%20their%20lates%202f45d316d80e47e98bb50642eb670fa6/Untitled.png)

Some of those updates are *major* releases. Running `npm update` won't help here. Major releases are *never* updated in this way because they (by definition) introduce breaking changes, and `npm` wants to save you trouble.

## [Leveraging npm-check-updates, you can upgrade all](https://nodejs.dev/en/learn/update-all-the-nodejs-dependencies-to-their-latest-version#update-all-packages-to-the-latest-version)

**Update All Packages to the Latest Version**

`package.json` dependencies to the latest version.

1. Now run `npm-check-updates` to upgrade all version hints in `package.json`, allowing installation of the new major versions:
    
    ```bash
    npm install -g npm-check-updates
    ```
    
2. Now run `npm-check-updates` to upgrade all version hints in `package.json` , allowing installation of the new major versions:
    
    ```bash
    ncu -u
    ```
    
3. Finally, run a standard install:
    
    ```bash
    npm install
    ```