# typeorm-seeding

Created: June 21, 2022 10:54 AM
Finished: No
Source: https://www.npmjs.com/package/typeorm-seeding

![typeorm-seeding%201a2adf8db3694c5e8d41a414b5823dc4/logo.png](typeorm-seeding%201a2adf8db3694c5e8d41a414b5823dc4/logo.png)

**A delightful way to seed test data into your database.**
 Inspired by the awesome framework [laravel](https://laravel.com/) in PHP and of the repositories from [pleerock](https://github.com/pleerock)Made with ❤️ by [Gery Hirschfeld](https://github.com/hirsch88) and [contributors](https://github.com/w3tecch/typeorm-seeding/graphs/contributors)

## ❯ Table of contents

- [Introduction](https://www.npmjs.com/package/typeorm-seeding#-introduction)
- [Installation](https://www.npmjs.com/package/typeorm-seeding#-installation)
- [Using Entity Factory](https://www.npmjs.com/package/typeorm-seeding#-using-entity-factory)
- [Seeding Data in Testing](https://www.npmjs.com/package/typeorm-seeding#-seeding-data-in-testing)
- [Changelog](https://www.npmjs.com/package/typeorm-seeding#-changelog)
- [License](https://www.npmjs.com/package/typeorm-seeding#-license)

## ❯ Introduction

Isn't it exhausting to create some sample data for your database, well this time is over!

How does it work? Just create a entity factory for your entities (models) and a seed script.

First create your TypeORM entites.

Then for each entity define a factory. The purpose of a factory is to create new entites with generate data.

> 
> 
> 
> Note: Factories can also be used to generate data for testing.
> 

And last but not least, create a seeder. The seeder can be called by the configured cli command `seed:run`. In this case it generates 10 pets with a owner (User).

> 
> 
> 
> Note: `seed:run` must be configured first. Go to [CLI Configuration](https://www.npmjs.com/package/typeorm-seeding#cli-configuration).
> 

## ❯ Installation

Before using this TypeORM extension please read the [TypeORM Getting Started](https://typeorm.io/#/) documentation. This explains how to setup a TypeORM project.

After that install the extension with `npm` or `yarn`.

Optional, install the type definitions of the `Faker` library.

To configure the path to your seeds and factories change the TypeORM config file(ormconfig.js or ormconfig.json).

> 
> 
> 
> The default paths are `src/database/{seeds,factories}/**/*{.ts,.js}`
> 

**ormconfig.js**

**.env**

```
TYPEORM_SEEDING_FACTORIES=src/factories/**/*{.ts,.js}
TYPEORM_SEEDING_SEEDS=src/seeds/**/*{.ts,.js}

```

Add the following scripts to your `package.json` file to configure the seed cli commands.

```
"scripts": {
  "seed:config": "ts-node ./node_modules/typeorm-seeding/dist/cli.js config"
  "seed:run": "ts-node ./node_modules/typeorm-seeding/dist/cli.js seed"
  ...
}

```

To execute the seed run `npm run seed:run` in the terminal.

> 
> 
> 
> Note: More CLI optios are [here](https://www.npmjs.com/package/typeorm-seeding#cli-options)
> 

Add the following TypeORM cli commands to the package.json to drop and sync the database.

```
"scripts": {
  ...
  "schema:drop": "ts-node ./node_modules/typeorm/cli.js schema:drop",
  "schema:sync": "ts-node ./node_modules/typeorm/cli.js schema:sync",
  ...
}

```

[Untitled](typeorm-seeding%201a2adf8db3694c5e8d41a414b5823dc4/Untitled%20Database%200666459098cf4b5fb0291d1553180fc4.csv)

A seeder class only contains one method by default `run`. Within this method, you may insert data into your database. For manually insertion use the [Query Builder](https://typeorm.io/#/select-query-builder) or use the [Entity Factory](https://www.npmjs.com/package/typeorm-seeding#-using-entity-factory)

> 
> 
> 
> Note. The seeder files will be executed alphabetically.
> 

Of course, manually specifying the attributes for each entity seed is cumbersome. Instead, you can use entity factories to conveniently generate large amounts of database records.

For all entities we want to create, we need to define a factory. To do so we give you the awesome [faker](https://github.com/marak/Faker.js/) library as a parameter into your factory. Then create your "fake" entity and return it. Those factory files should be in the `src/database/factories` folder and suffixed with `.factory` like `src/database/factories/user.factory.ts`.

[Untitled](typeorm-seeding%201a2adf8db3694c5e8d41a414b5823dc4/Untitled%20Database%2025c1a4471d644d2faa1f12c59f75faab.csv)

The define function creates a new enity factory.

Factory retrieves the defined factory function and returns the EntityFactory to start creating new enities.

Use the `.map()` function to alter the generated value before they get persisted.

Make and makeMany executes the factory functions and return a new instance of the given enity. The instance is filled with the generated values from the factory function, but not saved in the database.

**overrideParams** - Override some of the attributes of the enity.

the create and createMany method is similar to the make and makeMany method, but at the end the created entity instance gets persisted in the database.

**overrideParams** - Override some of the attributes of the enity.

The entity factories can also be used in testing. To do so call the `useSeeding` function, which loads all the defined entity factories.

Choose your test database wisley. We suggest to run your test in a sqlite in memory database.

However, if the test database is not in memory, than use the `--runInBand` flag to disable parallelizes runs.

Loads the defined entity factories.

Runs the given seeder class.

Connects to the database, drops it and recreates the schema.

Closes the open database connection.

> 
> 
> 
> Please fill free to add your open-source project here. This helps others to better understand the seeding technology.
> 

[Untitled](typeorm-seeding%201a2adf8db3694c5e8d41a414b5823dc4/Untitled%20Database%20d162a896956c43c09cec582df3d0c238.csv)

[Changelog](https://github.com/w3tecch/typeorm-seeding/releases)