# Working with folders in Node.js

Created: September 7, 2022 7:24 AM
Finished: Yes
Finished 📅: September 7, 2022
Order: 35
Source: https://nodejs.dev/en/learn/working-with-folders-in-nodejs/
Tags: #nodejs

The Node.js `fs` core module provides many handy methods you can use to work with folders.

Use `fs.access()` (and its promise-based `fsPromises.access()` counterpart) to check if the folder exists and Node.js can access it with its permissions.

Use `fs.mkdir()` or `fs.mkdirSync()` or `fsPromises.mkdir()` to create a new folder.

```jsx
const fs = require('fs');

const folderName = '/Users/joe/test';

try {
  if (!fs.existsSync(folderName)) {
    fs.mkdirSync(folderName);
  }
} catch (err) {
  console.error(err);
}
```

Use `fs.readdir()` or `fs.readdirSync()` or `fsPromises.readdir()` to read the contents of a directory.

This piece of code reads the content of a folder, both files and subfolders, and returns their relative path:

```jsx
const fs = require('fs');

const folderPath = '/Users/joe';

fs.readdirSync(folderPath);
```

You can get the full path:

```jsx
fs.readdirSync(folderPath).map(fileName => {
  return path.join(folderPath, fileName);
});
```

You can also filter the results to only return the files, and exclude the folders:

```jsx
const isFile = fileName => {
  return fs.lstatSync(fileName).isFile();
};

fs.readdirSync(folderPath)
  .map(fileName => {
    return path.join(folderPath, fileName);
  })
  .filter(isFile);
```

Use `fs.rename()` or `fs.renameSync()` or `fsPromises.rename()` to rename folder. The first parameter is the current path, the second the new path:

```jsx
const fs = require('fs');

fs.rename('/Users/joe', '/Users/roger', err => {
  if (err) {
    console.error(err);
  }
  // done
});
```

`fs.renameSync()` is the synchronous version:

```jsx
const fs = require('fs');

try {
  fs.renameSync('/Users/joe', '/Users/roger');
} catch (err) {
  console.error(err);
}
```

`fsPromises.rename()` is the promise-based version:

```jsx
const fs = require('fs/promises');

async function example() {
  try {
    await fs.rename('/Users/joe', '/Users/roger');
  } catch (err) {
    console.log(err);
  }
}
example();
```

Use `fs.rmdir()` or `fs.rmdirSync()` or `fsPromises.rmdir()` to remove a folder.

Removing a folder that has content can be more complicated than you need. You can pass the option `{ recursive: true }` to recursively remove the contents.

```jsx
const fs = require('fs');

fs.rmdir(dir, { recursive: true }, err => {
  if (err) {
    throw err;
  }

  console.log(`${dir} is deleted!`);
});
```

> NOTE: In Node v16.x the option recursive is deprecated for fs.rmdir of callback API, instead use fs.rm to delete folders that have content in them:
> 

```jsx
const fs = require('fs');

fs.rmdir(dir, { recursive: true }, err => {
  if (err) {
    throw err;
  }

  console.log(`${dir} is deleted!`);
});
```

Or you can install and make use of the `[fs-extra](https://www.npmjs.com/package/fs-extra)` module, which is very popular and well maintained. It's a drop-in replacement of the `fs` module, which provides more features on top of it.

In this case the `remove()` method is what you want.

Install it using

```bash
npm install fs-extra
```

and use it like this:

```jsx
const fs = require('fs-extra');

const folder = '/Users/joe';

fs.remove(folder, err => {
  console.error(err);
});
```

It can also be used with promises:

```jsx
fs.remove(folder)
  .then(() => {
    // done
  })
  .catch(err => {
    console.error(err);
  });
```

or with async/await:

```jsx
async function removeFolder(folder) {
  try {
    await fs.remove(folder);
    // done
  } catch (err) {
    console.error(err);
  }
}

const folder = '/Users/joe';
removeFolder(folder);
```