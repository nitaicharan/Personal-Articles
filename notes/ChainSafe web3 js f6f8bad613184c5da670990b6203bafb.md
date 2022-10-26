# ChainSafe/web3.js

Created: May 7, 2022 10:29 AM
Finished: No
Source: https://github.com/ChainSafe/web3.js
Tags: #tool

![ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/web3js.jpg](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/web3js.jpg)

# web3.js - Ethereum JavaScript API

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f646973636f72642f3539333635353337343436393636303637333f6c6162656c3d446973636f7264266c6f676f3d646973636f7264267374796c653d666c6174](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f646973636f72642f3539333635353337343436393636303637333f6c6162656c3d446973636f7264266c6f676f3d646973636f7264267374796c653d666c6174)

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f776562336a732d737461636b65786368616e67652d627269676874677265656e](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f776562336a732d737461636b65786368616e67652d627269676874677265656e)

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f762f776562332e737667](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f762f776562332e737667)

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f646d2f776562332e737667](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f646d2f776562332e737667)

![ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/badge.svg](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/badge.svg)

[https://camo.githubusercontent.com/bc999a5745c613fe6dcebb7795fdac122c284db11b10cdaf7c3089412d71d92c/68747470733a2f2f64617669642d646d2e6f72672f657468657265756d2f776562332e6a732f312e782f6465762d7374617475732e737667](https://camo.githubusercontent.com/bc999a5745c613fe6dcebb7795fdac122c284db11b10cdaf7c3089412d71d92c/68747470733a2f2f64617669642d646d2e6f72672f657468657265756d2f776562332e6a732f312e782f6465762d7374617475732e737667)

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f636f766572616c6c732e696f2f7265706f732f657468657265756d2f776562332e6a732f62616467652e7376673f6272616e63683d312e78](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f636f766572616c6c732e696f2f7265706f732f657468657265756d2f776562332e6a732f62616467652e7376673f6272616e63683d312e78)

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f6d61696e7461696e6564253230776974682d6c65726e612d6363303066662e737667](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f6d61696e7461696e6564253230776974682d6c65726e612d6363303066662e737667)

[ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f6170692e6e65746c6966792e636f6d2f6170692f76312f6261646765732f31666336343933332d643137302d343933392d386264622d3530386563643230353531392f6465706c6f792d737461747573](ChainSafe%20web3%20js%20f6f8bad613184c5da670990b6203bafb/68747470733a2f2f6170692e6e65746c6966792e636f6d2f6170692f76312f6261646765732f31666336343933332d643137302d343933392d386264622d3530386563643230353531392f6465706c6f792d737461747573)

This is the Ethereum [JavaScript API](http://web3js.readthedocs.io/) which connects to the [Generic JSON-RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC) spec.

You need to run a local or remote [Ethereum](https://www.ethereum.org/) node to use this library.

Please read the [documentation](http://web3js.readthedocs.io/) for more.

## Installation

### Node

```
npm install web3
```

### Yarn

```
yarn add web3
```

### In the Browser

Use the prebuilt `dist/web3.min.js`, or build using the [web3.js](https://github.com/ethereum/web3.js) repository:

```
npm run build
```

Then include `dist/web3.min.js` in your html file. This will expose `Web3` on the window object.

Or via jsDelivr CDN:

```
<script src="https://cdn.jsdelivr.net/npm/web3@latest/dist/web3.min.js"></script>
```

UNPKG:

```
<script src="https://unpkg.com/web3@latest/dist/web3.min.js"></script>
```

## Usage

```
// In Node.js
const Web3 = require('web3');

let web3 = new Web3('ws://localhost:8546');
console.log(web3);
> {
 eth: ... ,
 shh: ... ,
 utils: ...,
 ...
}
```

Additionally you can set a provider using `web3.setProvider()` (e.g. WebsocketProvider):

```
web3.setProvider('ws://localhost:8546');
// or
web3.setProvider(new Web3.providers.WebsocketProvider('ws://localhost:8546'));
```

There you go, now you can use it:

```
web3.eth.getAccounts().then(console.log);
```

### Usage with TypeScript

We support types within the repo itself. Please open an issue here if you find any wrong types.

You can use `web3.js` as follows:

```
import Web3 from 'web3';
import { BlockHeader, Block } from 'web3-eth' // ex. package types
const web3 = new Web3('ws://localhost:8546');
```

If you are using the types in a `commonjs` module, like in a Node app, you just have to enable `esModuleInterop` and `allowSyntheticDefaultImports` in your `tsconfig` for typesystem compatibility:

```
"compilerOptions": {
 "allowSyntheticDefaultImports": true,
 "esModuleInterop": true,
 ....
```

## Troubleshooting and known issues.

### Web3 and Create-react-app

If you are using create-react-app version >=5 you may run into issues building. This is because NodeJS polyfills are not included in the latest version of create-react-app.

### Solution

- Install react-app-rewired and the missing modules

If you are using yarn:

```
yarn add --dev react-app-rewired process crypto-browserify stream-browserify assert stream-http https-browserify os-browserify url buffer
```

If you are using npm:

```
npm install --save-dev react-app-rewired crypto-browserify stream-browserify assert stream-http https-browserify os-browserify url buffer process
```

- Create `config-overrides.js` in the root of your project folder with the content:

```
const webpack = require('webpack');

module.exports = function override(config) {
 const fallback = config.resolve.fallback || {};
 Object.assign(fallback, {
 "crypto": require.resolve("crypto-browserify"),
 "stream": require.resolve("stream-browserify"),
 "assert": require.resolve("assert"),
 "http": require.resolve("stream-http"),
 "https": require.resolve("https-browserify"),
 "os": require.resolve("os-browserify"),
 "url": require.resolve("url")
 })
 config.resolve.fallback = fallback;
 config.plugins = (config.plugins || []).concat([
 new webpack.ProvidePlugin({
 process: 'process/browser',
 Buffer: ['buffer', 'Buffer']
 })
 ])
 return config;
}
```

- Within `package.json` change the scripts field for start, build and test. Instead of `react-scripts` replace it with `react-app-rewired`

before:

```
"scripts": {
 "start": "react-scripts start",
 "build": "react-scripts build",
 "test": "react-scripts test",
 "eject": "react-scripts eject"
},
```

after:

```
"scripts": {
 "start": "react-app-rewired start",
 "build": "react-app-rewired build",
 "test": "react-app-rewired test",
 "eject": "react-scripts eject"
},
```

The missing Nodejs polyfills should be included now and your app should be functional with web3.

- If you want to hide the warnings created by the console:

In `config-overrides.js` within the `override` function, add:

```
config.ignoreWarnings = [/Failed to parse source map/];
```

### Web3 and Angular

### New solution

If you are using Angular version >11 and run into an issue building, the old solution below will not work. This is because polyfills are not included in the newest version of Angular.

- Install the required dependencies within your angular project:

```
npm install --save-dev crypto-browserify stream-browserify assert stream-http https-browserify os-browserify
```

- Within `tsconfig.json` add the following `paths` in `compilerOptions` so Webpack can get the correct dependencies

```
{
 "compilerOptions": {
 "paths" : {
 "crypto": ["./node_modules/crypto-browserify"],
 "stream": ["./node_modules/stream-browserify"],
 "assert": ["./node_modules/assert"],
 "http": ["./node_modules/stream-http"],
 "https": ["./node_modules/https-browserify"],
 "os": ["./node_modules/os-browserify"],
 }
}
```

- Add the following lines to `polyfills.ts` file:

```
import { Buffer } from 'buffer';

(window as any).global = window;
global.Buffer = Buffer;
global.process = {
 env: { DEBUG: undefined },
 version: '',
 nextTick: require('next-tick')
} as any;
```

### Old solution

If you are using Ionic/Angular at a version >5 you may run into a build error in which modules `crypto` and `stream` are `undefined`

a work around for this is to go into your node-modules and at `/angular-cli-files/models/webpack-configs/browser.js` change the `node: false` to `node: {crypto: true, stream: true}` as mentioned [here](https://github.com/ethereum/web3.js/issues/2260#issuecomment-458519127)

Another variation of this problem was an [issue opned on angular-cli](https://github.com/angular/angular-cli/issues/1548)

## Documentation

Documentation can be found at [ReadTheDocs](http://web3js.readthedocs.io/).

## Building

### Requirements

- [Node.js](https://nodejs.org/)
- [npm](https://www.npmjs.com/)

```
sudo apt-get update
sudo apt-get install nodejs
sudo apt-get install npm
```

### Building (webpack)

Build the web3.js package:

```
npm run build
```

### Testing (mocha)

```
npm test
```

### Contributing

Please follow the [Contribution Guidelines](https://github.com/ChainSafe/web3.js/blob/1.x/CONTRIBUTIONS.md) and [Review Guidelines](https://github.com/ChainSafe/web3.js/blob/1.x/REVIEW.md).

This project adheres to the [Release Guidelines](https://github.com/ChainSafe/web3.js/blob/1.x/REVIEW.md).

### Community

- [Discord](https://discord.gg/pb3U4zE8ca)
- [StackExchange](https://ethereum.stackexchange.com/questions/tagged/web3js)

### Similar libraries in other languages

- Haskell: [hs-web3](https://github.com/airalab/hs-web3)
- Java: [web3j](https://github.com/web3j/web3j)
- PHP: [web3.php](https://github.com/web3p/web3.php)
- Purescript: [purescript-web3](https://github.com/f-o-a-m/purescript-web3)
- Python: [Web3.py](https://github.com/ethereum/web3.py)
- Ruby: [ethereum.rb](https://github.com/EthWorks/ethereum.rb)
- Scala: [web3j-scala](https://github.com/mslinn/web3j-scala)

## Semantic versioning

This project follows [semver](https://semver.org/) as closely as possible **from version 1.3.0 onwards**. Earlier minor version bumps [might](https://github.com/ethereum/web3.js/issues/3758) have included breaking behavior changes.