# Buildtime vs runtime environment variables with Next.js and Docker

Created: January 20, 2022 9:55 AM
Finished: No
Source: https://www.saltycrane.com/blog/2021/04/buildtime-vs-runtime-environment-variables-nextjs-docker/

For a Next.js app, **buildtime** environment variables are variables that are used when the `next build` command runs. **Runtime** variables are variables used when the `next start` command runs.

Below are ways to set buildtime and rutime environment variables with Docker and ways to use buildtime and runtime environment variables with Next.js. Note the `Dockerfile` is written for simplicity to illustrate the examples. For a more optimized Next.js Docker build see my [Docker multi-stage CI example](https://www.saltycrane.com/blog/2021/04/nextjs-gitlab-cicd-docker-multi-stage-example/).

### Methods for setting environment variables with Docker

```
ARG
```

### Methods for using environment variables in Next.js

[Untitled](Buildtime%20vs%20runtime%20environment%20variables%20with%20Ne%2092511647d86e456ba8e9a3db47d22d02/Untitled%20Database%201e340a85e3c94837a6c0973b05aad064.csv)

### Assume this `package.json` for the examples below

```
{
  "scripts": {
    "build": "next build",
    "dev": "next",
    "start": "next start"
  },
  "dependencies": {
    "next": "^10.0.9",
    "react": "^17.0.2",
    "react-dom": "^17.0.2"
  }
}

```

### Setting static environment variables for buildtime and runtime

Environment variables can be specified with the `ENV` instruction in a `Dockerfile`. Below `MY_VAR` will be available to both `next build` and `next start`. For more information see [https://docs.docker.com/engine/reference/builder/#env](https://docs.docker.com/engine/reference/builder/#env)

**`Dockerfile`**

```
FROM node:14-alpine

ENV MY_VAR=cake

WORKDIR /app
COPY . ./
RUN npm install
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

```

**Docker build**

```
docker build -t mytag .

```

**Docker run**

```
docker run mytag

```

### Setting dynamic buildtime environment variables

Dynamic environment variables can be passed to the `docker build` command using `--build-arg` and used in the `Dockerfile` with the `ARG` statement. Below `MY_VAR` is an environment variable available to `next build`.

Note that `MY_VAR` is not available to `next start`. `ARG` statements act like `ENV` statements in that they are treated like environment variables during `docker build`, but they are not persisted in the image. To make them available during `docker run` (and `next start`) set the value using `ENV` (see the next example).

For more information see [https://docs.docker.com/engine/reference/builder/#arg](https://docs.docker.com/engine/reference/builder/#arg)

**`Dockerfile`**

```
FROM node:14-alpine

ARG MY_VAR

WORKDIR /app
COPY . ./
RUN npm install
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

```

**Docker build**

```
docker build --build-arg MY_VAR=cake -t mytag .

```

**Docker run**

```
docker run mytag

```

### Setting dynamic buildtime environment variables that are available at runtime also

The variable in the previous example, set using `ARG`, is not persisted in the Docker image so it is not available at runtime. To make it available at runtime, copy the value from `ARG` to `ENV`.

**`Dockerfile`**

```
FROM node:14-alpine

ARG MY_VAR
ENV MY_VAR=$MYVAR

WORKDIR /app
COPY . ./
RUN npm install
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

```

**Docker build**

```
docker build --build-arg MY_VAR=cake -t mytag .

```

**Docker run**

```
docker run mytag

```

### Setting dynamic runtime environment variables

Dynamic environment variables can be passed to `docker run` using the `--env` flag. These will not be available to `next build` but they will be available to `next start`. For more information see [https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file)

**`Dockerfile`**

```
FROM node:14-alpine
WORKDIR /app
COPY . ./
RUN npm install
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

```

**Docker build**

```
docker build -t mytag .

```

**Docker run**

```
docker run --env MY_VAR=cake mytag

```

### Using buildtime environment variables

To use buildtime environment variables in Next.js code, set them using `env` in `next.config.js`. Then access them via `process.env` in your app code. NOTE: `process.env` cannot be destructured or used with dynamic property access. Next.js does a string substituion at build time using the webpack [DefinePlugin](https://webpack.js.org/plugins/define-plugin/). For more information see [https://nextjs.org/docs/api-reference/next.config.js/environment-variables](https://nextjs.org/docs/api-reference/next.config.js/environment-variables)

**`next.config.js`**

```
module.exports = {
  env: {
    MY_VAR: process.env.MY_VAR
  }
}

```

**`my-app-file.js`**

```
console.log(process.env.MY_VAR)

```

### Using runtime environment variables (client-side or server-side)

To use runtime environment variables (client-side or server-side), set them using `publicRuntimeConfig` in `next.config.js`. Then access them using `getConfig` from `next/config`. NOTE: this only works for Next.js pages where server-side rendering (SSR) is used. i.e. the page must use `[getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering)` or `[getInitialProps](https://nextjs.org/docs/api-reference/data-fetching/getInitialProps)`. For more information see [https://nextjs.org/docs/api-reference/next.config.js/runtime-configuration](https://nextjs.org/docs/api-reference/next.config.js/runtime-configuration)

**`next.config.js`**

```
module.exports = {
  publicRuntimeConfig: {
    MY_VAR: process.env.MY_VAR
  }
}

```

**`my-app-file.js`**

```
import getConfig from "next/config";
const { publicRuntimeConfig } = getConfig();
console.log(publicRuntimeConfig.MY_VAR)

```

### Using runtime environment variables (server-side only)

To use runtime environment variables (server-side only), set them using `serverRuntimeConfig` in `next.config.js`. Then access them using `getConfig` from `next/config`. For more information see [https://nextjs.org/docs/api-reference/next.config.js/runtime-configuration](https://nextjs.org/docs/api-reference/next.config.js/runtime-configuration)

NOTE: this applies to to files Next.js "builds". Server run files not processed by Next.js can use `process.env` to access environment variables. See below.

**`next.config.js`**

```
module.exports = {
  serverRuntimeConfig: {
    MY_VAR: process.env.MY_VAR
  }
}

```

**`my-app-file.js`**

```
import getConfig from "next/config";
const { serverRuntimeConfig } = getConfig();
console.log(serverRuntimeConfig.MY_VAR)

```

### Using runtime environment variables server-side (not processed by Next.js)

For files not processed by Next.js (`next build`) (e.g. a `server.js` file run by `node`), runtime environment variables can be accessed on the server via `process.env`. NOTE: "runtime" variables here means variables used when the Node.js process runs. For more information see [https://nodejs.org/docs/latest-v14.x/api/process.html#process_process_env](https://nodejs.org/docs/latest-v14.x/api/process.html#process_process_env)

**`server.js`**

```
console.log(process.env.MY_VAR)

```

### Next.js `assetPrefix`

If the [Next.js `assetPrefix`](https://nextjs.org/docs/api-reference/next.config.js/cdn-support-with-asset-prefix) is set in `next.config.js` using an environment variable, the environment variable should be set at buildtime for [Next.js static pages](https://nextjs.org/docs/advanced-features/automatic-static-optimization) but set at runtime for [server rendered pages](https://nextjs.org/docs/basic-features/pages#server-side-rendering).

**`next.config.js`**

```
module.exports = {
  assetPrefix: process.env.MY_ASSET_PREFIX
}

```