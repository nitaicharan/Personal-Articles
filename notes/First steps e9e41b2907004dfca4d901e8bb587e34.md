# First steps

Created: October 21, 2021 12:14 PM
Finished: Yes
Finished 📅: October 24, 2021
Source: https://docs.nestjs.com/first-steps
Tags: #nestjs

- [x]  [First steps](https://docs.nestjs.com/first-steps#first-steps)

The first step section is a summary of would the chapter approach as core fundamentals and build a basic CRUD application

- [x]  [Language](https://docs.nestjs.com/first-steps#language)

This section explains there is the possibility to use on Nest both JavaScript and TypeScript. Because Nest is built above Node.js and it takes advantage of the latest JavaScript features it needs a Babel compiler.

- [x]  [Prerequisites](https://docs.nestjs.com/first-steps#prerequisites)

This section explains that Nest require a Node.js ≥ 10.13.0 installed on your operation system to work.

- [x]  [Setup](https://docs.nestjs.com/first-steps#setup)

This section says that in order to create a Nest application it needs to be installed in your operation system. One of the easiest way is though a JavaScript package manage as `npm` and `yarn` how it shows below:

```bash
$ npm i -g @nestjs/cli
```

and after creates a project:

```bash
$ nest new project-name
```

The project `project-name` will contains boilerplate files and the scaffold main files will be be structure in the `src/` folder with the follow structure:

```
src
   |--app.controller.spec.ts
   |--app.controller.ts
   |--app.module.ts
   |--app.service.ts
   L--main.ts
```

The follow table explains more about each file purpose; 

[Untitled](First%20steps%20e9e41b2907004dfca4d901e8bb587e34/Untitled%20Database%200b2c75f0e70c4d56b94e13ea9daa247c.csv)

The Nest project bootstrapped in the `main.js` which contains a `async` function changed for it.

Follows the `main.js` file content

```tsx
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

The function `NestFactory` exposes few methods to allow creating an application instance. In the example above it's using the `create()` method.

If it's necessary the application type instance it's possible to type the `create()` object return as `INestApplication` interface.

In the example above is simple creating an application instance and after starting up a HTTP listener which allows a await inbound HTTP requests.

Notes that is highly recommended to follow the scaffold convention of keeping each module in its own dedicated directory

- [x]  **[Platform](https://docs.nestjs.com/first-steps#platform)**

Nest is a platform-agnostic framework and it's bring the advantage of a platform independence.

It allows the developer to reuse logical parts of application in differences projects and codes.

Because of it Nest  is able to work with any Node HTTP framework once an adapter is created.

The most common HTTP frameworks `express` and `fastify`

[Untitled](First%20steps%20e9e41b2907004dfca4d901e8bb587e34/Untitled%20Database%20fa86c78235ae4df0bd63aad05ddfd279.csv)

To switch between frame express and fastify frameworks simple use `NestExpressApplication` and `NestFastifyApplication` to create the application instance. Afterwords the application will contains few methods available exclusively for that specific platform

```tsx
const app = await NestFactory.create<NestExpressApplication>(AppModule);
```

- [x]  [Running the application](https://docs.nestjs.com/first-steps#running-the-application)
- In order to boot up the application and start to listen for inbound HTTP requests on port defined in `src/main.js` run the script `start` in the `package.json` file

```bash
npm run start
```