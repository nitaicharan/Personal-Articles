# Setup dotenv to Access Environment Variables in Angular

Created: May 10, 2022 5:27 PM
Finished: No
Source: https://javascript.plainenglish.io/setup-dotenv-to-access-environment-variables-in-angular-9-f06c6ffb86c0
Tags: #angular

[Setup%20dotenv%20to%20Access%20Environment%20Variables%20in%20An%20cb41f1930e8d41bcba97bdebfa5ef720/0G5FUUfVNIS0fyBbk](Setup%20dotenv%20to%20Access%20Environment%20Variables%20in%20An%20cb41f1930e8d41bcba97bdebfa5ef720/0G5FUUfVNIS0fyBbk)

Photo by [Benjamin Massello](https://unsplash.com/@doctortinieblas?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

This article is relevant for the scenario when you need to deploy the same application but with a different configuration file.

Googling for help on setting up environment variables for Angular is a nightmare; Either the article was too old or it was for AngularJS or it was simply using an ancient version of the Angular CLI.

It took me a while but I managed to find a proper workaround, which turned out to be quite interesting.

# **The Problem 👨‍🏫**

In Angular, we have our `environment.ts` and `environment.prod.ts` files defined in the *src/environments* folder. The `environment.ts` file (shown below) is where we usually keep our environment variables by convention, as the Angular compiler looks for these files before the build process.

```
export const environment = {
   production: false,
   API_KEY: '1234_API_KEY_5678',
   ANOTHER_API_SECRET: '__ANOTHER_SECRET__'
};
```

We use this environment object in our components/services like so:

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { environment } from '../environments/environment';@Injectable({
   providedIn: 'root'
})
export class SomeService {
   apiKey: string;   constructor(private http: HttpClient) {
      this.apiKey = environment.API_KEY;
      ...
   }
   ...
}
```

Now, the problem is, these environment files are required by Angular for the build process, meaning they have to be pushed to the repository so that others can also install the project locally. If you have your API keys explicitly written in these files, consider those keys compromised, because anyone who has access to your repository can see them.

# The Solution 🔐

We’ll use a lightweight package named [dotenv](https://www.npmjs.com/package/dotenv).

> Dotenv is a zero-dependency module that loads environment variables from a .env file into process.env.
> 

Basically what it does is it takes variables defined in a *.env* file, and injects them into Node’s [process.env](https://nodejs.org/docs/latest/api/process.html#process_process_env) object. It’s in this .env file where you’ll keep all your secret credentials and sensitive information, and this file should not be pushed to your remote repository.

```
API_KEY=1234_API_KEY_5678
ANOTHER_API_SECRET=__ANOTHER__SECRET__
```

A simple key-value pair creates these environment variables for you.

Now using it just takes one line of code:

```
require('dotenv').config();
```

Believe me that’s it.

I thought it would be okay if I did this in the *environment.ts* file:

```
export const environment = {
   production: false,
   API_KEY: process.env.API_KEY,
   ANOTHER_API_SECRET: process.env.ANOTHER_API_SECRET
};
```

However, the Angular compiler treats these *environment.*.ts* files as static, and it wouldn’t compile.

***Workaround:*** Write a script to generate the *environment* file dynamically before the build!

# The Process 🧪

***TL;DR:***

1. Create a *.env* file at the root of your project folder and populate it with your variables.
2. Write a script which creates the required environment file (*environment.ts* for development and *environment.prod.ts* for production) and populates it with the variables from your *.env* file (available in `process.env`).
3. Run the script before running `ng serve` or `ng build`.

So, installing yargs and dotenv shouldn’t be a problem:

```
npm install --save-dev yargs dotenv
```

I’ll use the *.env* file from above with the same contents:

```
API_KEY=1234_API_KEY_5678
ANOTHER_API_SECRET=__ANOTHER__SECRET__
```

The script which does all the magic is a relatively simple one. Create a *setenv.ts* inside a *scripts* folder on the root of your project.

```
const { writeFile } = require('fs');
const { argv } = require('yargs');// read environment variables from .env file
require('dotenv').config();// read the command line arguments passed with yargs
const environment = argv.environment;
const isProduction = environment === 'prod';const targetPath = isProduction
   ? `./src/environments/environment.prod.ts`
   : `./src/environments/environment.ts`;// we have access to our environment variables
// in the process.env object thanks to dotenv
const environmentFileContent = `
export const environment = {
   production: ${isProduction},
   API_URL: "${process.env.API_URL}",
   ANOTHER_API_SECRET: "${process.env.ANOTHER_API_SECRET}"
};
`;// write the content to the respective file
writeFile(targetPath, environmentFileContent, function (err) {
   if (err) {
      console.log(err);
   }   console.log(`Wrote variables to ${targetPath}`);
}};
```

Easy? Now we just need to modify our start and build script so that these files are generated dynamically. Do this in the *package.json* file:

```
{
   ...
   "scripts": {
      "ng": "ng",
      "config": "ts-node ./scripts/setenv.ts",
      "start": "npm run config -- --environment=dev && ng serve",
      "build": "npm run config -- --environment=prod && ng build",
      ...
   },
   ...
}
```

You can tweak the script a little bit by adding a check to see if all your environment variables have been passed or not.

```
// read the command line arguments passed with yargs
const environment = argv.environment;
const isProduction = environment === 'prod';if (!process.env.API_KEY || !process.env.ANOTHER_API_KEY) {
   console.error('All the required environment variables were not provided!');
   process.exit(-1);
}
```

If you don’t specify the proper environment variables then this process will automatically exit!

Remember that you should not push the *.env* file to a remote repository, or the environment files that have been generated! Simply keep a template on the repo and share credentials only with trusted people.

Cheers! ✨

*Originally published at [https://www.ninadsubba.in](https://www.ninadsubba.in/blog/setup-dotenv-to-access-environment-variables-in-angular-9).*

# A note from the Plain English team

Did you know that we have four publications? Show some love by giving them a follow: **[JavaScript in Plain English](https://medium.com/javascript-in-plain-english)**, **[AI in Plain English](https://medium.com/ai-in-plain-english)**, **[UX in Plain English](https://medium.com/ux-in-plain-english)**, **[Python in Plain English](https://medium.com/python-in-plain-english)** — thank you and keep learning!

We’ve also launched a YouTube and would love for you to support us by **[subscribing to our Plain English channel](https://www.youtube.com/channel/UCtipWUghju290NWcn8jhyAw)**