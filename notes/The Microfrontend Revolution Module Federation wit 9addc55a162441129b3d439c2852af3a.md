# The Microfrontend Revolution: Module Federation with Angular

Created: April 20, 2022 3:47 PM
Finished: Yes
Finished 📅: May 5, 2022
Source: https://www.angulararchitects.io/aktuelles/the-microfrontend-revolution-part-2-module-federation-with-angular/
Tags: #angular

## With Module Federation we can use Angular and its router to lazy load separately compiled and deployed microfrontends.

> 
> 
> 
> 2021-08-08: Updated for Angular CLI 12.x 2021-12-23: Updated for Angular CLI 13.1.x and above
> 
> Big thanks to [Zack Jackson](https://twitter.com/ScriptedAlchemy), the mastermind behind Module Federation, who helped me bypassing some pitfalls.
> 

**Important**: This article is written for Angular and **Angular CLI 13.1** and higher. Make sure you have a fitting version if you try out the examples outlined here! For more details on the differences/ migration to Angular 13.1 please see this [migration guide](https://github.com/angular-architects/module-federation-plugin/blob/main/migration-guide.md).

In my [previous article](https://www.angulararchitects.io/aktuelles/the-microfrontend-revolution-module-federation-in-webpack-5/), I’ve shown how to use Module Federation which is part of webpack beginning with version 5 to implement microfrontends. This article brings Angular into play and shows how to create an Angular-based microfrontend shell using the router to lazy load a separately compiled, and deployed microfrontend.

Besides using Angular, the result looks similar as in the previous article:

![The%20Microfrontend%20Revolution%20Module%20Federation%20wit%209addc55a162441129b3d439c2852af3a/shell.png](The%20Microfrontend%20Revolution%20Module%20Federation%20wit%209addc55a162441129b3d439c2852af3a/shell.png)

The loaded microfrontend is shown within the red dashed border. Also, the microfrontend can be used without the shell:

![The%20Microfrontend%20Revolution%20Module%20Federation%20wit%209addc55a162441129b3d439c2852af3a/standalone.png](The%20Microfrontend%20Revolution%20Module%20Federation%20wit%209addc55a162441129b3d439c2852af3a/standalone.png)

The [source code](https://github.com/manfredsteyer/module-federation-plugin-example) of the used example can be found in my [GitHub account](https://github.com/manfredsteyer/module-federation-plugin-example).

## Activating Module Federation for Angular Projects

The case study presented here assumes that both, the shell and the microfrontend are projects in the same Angular workspace.

- What has to be done to set up the CLI Angular projects to use module federation to build them?
    
    For getting started, we need to tell the CLI to use module federation when building them. 
    

However, as the CLI shields webpack from us, we need a custom builder.

- How to use Webpack in angular projects?
    
    The package [@angular-architects/module-federation](https://www.npmjs.com/package/@angular-architects/module-federation) provides such a custom builder.
    

To get started, you can just "ng add" it to your projects:

```bash
ng add @angular-architects/module-federation --project shell --port 5000
ng add @angular-architects/module-federation --project mfe1 --port 3000
```

While it’s obvious that the project `shell` contains the code for the `shell`, `mfe1` stands for *Micro Frontend 1*.

The command shown does several things:

- Generating the skeleton of an `webpack.config.js` for using module federation
- Installing a custom builder making webpack within the CLI use the generated `webpack.config.js`.
- Assigning a new port for ng serve so that several projects can be served simultaneously.

Please note that the `webpack.config.js` is only a **partial** webpack configuration. It only contains stuff to control module federation. The rest is generated by the CLI as usual.

## The Shell (aka Host)

Let’s start with the shell which would also be called the host in module federation. It uses the router to lazy load a `FlightModule`:

```tsx
export const APP_ROUTES: Routes = [
    {
      path: '',
      component: HomeComponent,
      pathMatch: 'full'
    },
    {
      path: 'flights',
      loadChildren: () => import('mfe1/Module').then(m => m.FlightsModule)
    },
];
```

However, the path `mfe1/Module` which is imported here, **does not exist** within the shell. It’s just a virtual path pointing to another project.

To ease the TypeScript compiler, we need a typing for it:

Also, we need to tell webpack that all paths starting with `mfe1` are pointing to an other project.

- How set up Webpack path to an other project?
    
    This can be done by using the `ModuleFederationPlugin` in the generated `webpack.config.js`:
    

```jsx
const ModuleFederationPlugin = require("webpack/lib/container/ModuleFederationPlugin");
const mf = require("@angular-architects/module-federation/webpack");
const path = require("path");
const share = mf.share;

[...]

module.exports = {
  output: {
    uniqueName: "shell",
    publicPath: "auto"
  },
  optimization: {
    runtimeChunk: false
  },
  resolve: {
    alias: {
      ...sharedMappings.getAliases(),
    }
  },
  experiments: {
     outputModule: true
  },
  plugins: [
    new ModuleFederationPlugin({
      library: {
				type: "module"
			},
      remotes: {
          "mfe1": "mfe1@http://localhost:3000/remoteEntry.js",
      },
      shared: share({
        "@angular/core": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
        "@angular/common": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
        "@angular/router": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
        "@angular/common/http": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
        ...sharedMappings.getDescriptors()
      }),
    }),
    sharedMappings.getPlugin(),
  ],
};
```

- Why `experiments` section is needed for Webpack configuration?
    
    The `experiments` section is temporarily needed to activate a bug fix in webpack.
    
- What `remote` section maps?
    
    The `remotes` section maps the internal name `mfe1` to the same one defined within the separately compiled microfrontend.
    
- What `remote` section point at?
    
    It also points to the path where the remote can be found — or to be more precise: to its remote entry.
    
- What is a remote entry generated in the project configured and build with Webpack?
    
    This is a tiny file generated by webpack when building the remote. Webpack loads it at runtime to get all the information needed for interacting with the microfrontend.
    

While specifying the remote entry’s URL that way is convenient for development, we need a more dynamic approach for production. Fortunately, there are several options for doing this. One option is presented in a below sections.

- What share section contains?
    
    The property `shared` contains the names of libraries our shell shares with the microfrontend(s).
    
- What makes Webpack a runtime error in case of incompatibles libraries version?
    
    The combination of `singleton: true` and `strictVersion: true` makes webpack emit a runtime error when the shell and the micro frontend(s) need different incompatible versions (e. g. two different major versions).
    
- What happen if skipped `strictVersion` or set it to `false`?
    
    If we skipped `strictVersion` or set it to `false`, webpack would only emit a warning at runtime. [More information](https://www.angulararchitects.io/aktuelles/getting-out-of-version-mismatch-hell-with-module-federation/) about dealing with version mismatches can found in a [further article of this series](https://www.angulararchitects.io/aktuelles/getting-out-of-version-mismatch-hell-with-module-federation/).
    

The setting `requiredVersion: 'auto'` is a little extra provided by the `@angular-architects/module-federation` plugin. It looks up the used version in your `package.json`. This prevents several issues.

> The helper function share used in this generated configuration replaces the value `'auto'` with the version found in your `package.json`.
> 

In addition to the settings for the `ModuleFederationPlugin`, we also need to place some options in the `output` section.

- What `uniqueName` option is used for?
    
    The `uniqueName` is used to represent the host or remote in the generated bundles. By default, webpack uses the name from `package.json` for this. In order to avoid name conflicts when using monorepos with several applications, it is recommended to set the `uniqueName` manually.
    

## The Microfrontend (aka Remote)

The microfrontend — also referred to as a *remote* with terms of module federation — looks like an ordinary Angular application. It has routes defined within in the `AppModule`:

```tsx
export const APP_ROUTES: Routes = [
    { path: '', component: HomeComponent, pathMatch: 'full'}
];
```

Also, there is a `FlightsModule`:

```tsx
@NgModule({
  imports: [
    CommonModule,
    RouterModule.forChild(FLIGHTS_ROUTES)
  ],
  declarations: [
    FlightsSearchComponent
  ]
})
export class FlightsModule { }
```

This module has some routes of its own:

```tsx
export const FLIGHTS_ROUTES: Routes = [
    {
      path: 'flights-search',
      component: FlightsSearchComponent
    }
];
```

- How to export a module from a micro fronted to be load in the shell project?
    
    In order to make it possible to load the `FlightsModule` into the shell, we also need to reference the `ModuleFederationPlugin` in the remote’s webpack configuration:
    

```tsx
const ModuleFederationPlugin = require("webpack/lib/container/ModuleFederationPlugin");
const mf = require("@angular-architects/module-federation/webpack");
const path = require("path");

const share = mf.share;

[...]

module.exports = {
  output: {
    uniqueName: "mfe1",
    publicPath: "auto"
  },
  optimization: {
    runtimeChunk: false
  },
  resolve: {
    alias: {
      ...sharedMappings.getAliases(),
    }
  },
  experiments: {
    outputModule: true
  },
  plugins: [
    new ModuleFederationPlugin({
        library: { type: "module" },

        name: "mfe1",
        filename: "remoteEntry.js",
        exposes: {
            './Module': './projects/mfe1/src/app/flights/flights.module.ts',
        },
        shared: share({
          "@angular/core": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
          "@angular/common": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
          "@angular/router": { singleton: true, strictVersion: true, requiredVersion: 'auto' },
          "@angular/common/http": { singleton: true, strictVersion: true, requiredVersion: 'auto' },

          ...sharedMappings.getDescriptors()
        })

    }),
    sharedMappings.getPlugin(),
  ],
};
```

The configuration shown here exposes the `FlightsModule` under the public name `Module`. The section `shared` points to the libraries shared with the shell.

## Trying it out

To try everything out, we just need to start the shell and the microfrontend:

```
ng serve shell -o
ng serve mfe1 -o
```

Then, when clicking on `Flights` in the shell, the micro frontend is loaded:

![The%20Microfrontend%20Revolution%20Module%20Federation%20wit%209addc55a162441129b3d439c2852af3a/schema.png](The%20Microfrontend%20Revolution%20Module%20Federation%20wit%209addc55a162441129b3d439c2852af3a/schema.png)

> **Hint:** To start several projects with one command, you can use the npm package [concurrently](https://www.npmjs.com/package/concurrently).
> 

## A little further detail

Ok, that worked quite well. But have you had a look into your `main.ts`?

It just looks like this:

```tsx
import('./bootstrap').catch(err => console.error(err));
```

The code you normally find in the file `main.ts` was moved to the `bootstrap.ts` file loaded here. All of this was done by the `@angular-architects/module-federation` plugin.

While this doen’t seem to make a lot of sense at first sight, it’s a typical pattern you find in Module Federation-based applications. The reason is that Module Federation needs to decide which version of a shared library to load. If the shell, for instance, is using version 12.0 and one of the micro frontends is already built with version 12.1, it will decide to load the latter one.

To look up the needed meta data for this decision, Module Fedaration squeezes itself into dynamic imports like this one here. Other than the more tradtional static imports, dynamic imports are asynchronous. Hence, Module Federation can decide on the versions to use and actually load them.

More details on this can be found in [another article of this series](https://www.angulararchitects.io/aktuelles/getting-out-of-version-mismatch-hell-with-module-federation/).

## Conclusion and Evaluation

The implementation of microfrontends has so far involved numerous tricks and workarounds. Webpack Module Federation finally provides a simple and solid solution for this. To improve performance, libraries can be shared and strategies for dealing with incompatible versions can be configured.

It is also interesting that the microfrontends are loaded by Webpack under the hood. There is no trace of this in the source code of the host or the remote. This simplifies the use of module federation and the resulting source code, which does not require additional microfrontend frameworks.

However, this approach also puts more responsibility on the developers. For example, you have to ensure that the components that are only loaded at runtime and that were not yet known when compiling also interact as desired.

One also has to deal with possible version conflicts. For example, it is likely that components that were compiled with completely different Angular versions will not work together at runtime. Such cases must be avoided with conventions or at least recognized as early as possible with integration tests.

## What’s next? More on Architecture!

So far, we’ve seen that Module Federation is a strightforward solution for creating Micro Frontends on top of Angular. However, when dealing with it, several additional questions come in mind:

- According to which criteria can we sub-divide a huge application into micro frontends?
- Which access restrictions make sense?
- Which proven patterns should we use?
- How can we avoid pitfalls when working with Module Federation?
- Which advanced scenarios are possible?

Our free eBook (about 100 pages) covers all these questions and more:

Feel free to [download it here](https://www.angulararchitects.io/book) now!