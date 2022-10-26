# npm dependencies and devDependencies

Created: August 30, 2022 9:35 AM
Finished: Yes
Finished 📅: August 30, 2022
Order: 23
Source: https://nodejs.dev/en/learn/npm-dependencies-and-devdependencies/
Tags: #nodejs

When you install an npm package using `npm install <package-name>`, you are installing it as a **dependency**.

The package is automatically listed in the package.json file, under the `dependencies` list (as of npm 5. previously, you had to manually specify `--save`).

When you add the `-D` flag, or `--save-dev`, you are installing it as a development dependency, which adds it to the `devDependencies` list.

Development dependencies are intended as development-only packages, that are unneeded in production. For example testing packages, webpack or Babel.

When you go in production, if you type `npm install` and the folder contains a `package.json` file, they are installed, as npm assumes this is a development deploy.

You need to set the `--production` flag (`npm install --production`) to avoid installing those development dependencies.