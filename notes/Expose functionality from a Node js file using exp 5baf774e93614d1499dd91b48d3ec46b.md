# Expose functionality from a Node.js file using exports

Created: August 18, 2022 8:12 AM
Finished: Yes
Finished 📅: August 18, 2022
Order: 12
Source: https://nodejs.dev/en/learn/expose-functionality-from-a-nodejs-file-using-exports
Tags: #nodejs

Node.js has a built-in module system.

A Node.js file can import functionality exposed by other Node.js files.

When you want to import something you use

```jsx
const library = require('./library');
```

to import the functionality exposed in the `library.js` file that resides in the current file folder.

In this file, functionality must be exposed before it can be imported by other files.

Any other object or variable defined in the file by default is private and not exposed to the outer world.

This is what the `module.exports` API offered by the `[module` system](https://nodejs.org/api/modules.html) allows us to do.

When you assign an object or a function as a new `exports` property, that is the thing that's being exposed, and as such, it can be imported in other parts of your app, or in other apps as well.

You can do so in 2 ways.

The first is to assign an object to `module.exports`, which is an object provided out of the box by the module system, and this will make your file export *just that object*:

```jsx
// car.js
const car = {
  brand: 'Ford',
  model: 'Fiesta',
};

module.exports = car;
```

```jsx
const car = require('./car');
```

The second way is to add the exported object as a property of `exports`. This way allows you to export multiple objects, functions or data:

```jsx
const car = {
  brand: 'Ford',
  model: 'Fiesta',
};

exports.car = car;
```

or directly

```jsx
exports.car = {
  brand: 'Ford',
  model: 'Fiesta',
};
```

And in the other file, you'll use it by referencing a property of your import:

```jsx
const items = require('./car');

const { car } = items;
```

or you can use a destructuring assignment:

```jsx
const { car } = require('./car');
```

What's the difference between `module.exports` and `exports`?

The first exposes the object it points to. The latter exposes *the properties* of the object it points to.

`require` will always return the object that `module.exports` points to.

```jsx
// car.js
exports.car = {
  brand: 'Ford',
  model: 'Fiesta',
};

module.exports = {
  brand: 'Tesla',
  model: 'Model S',
};

// app.js
const tesla = require('./car');
const ford = require('./car').car;

console.log(tesla, ford);
```

This will print `{ brand: 'Tesla', model: 'Model S' } undefined` since the `require` function's return value has been updated to the object that `module.exports` points to, so *the property* that `exports` added can't be accessed.