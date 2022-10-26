# Why we have banned default exports in Javascript and you should do the same

Created: June 4, 2022 8:19 PM
Finished: Yes
Finished 📅: June 4, 2022
Source: https://blog.neufund.org/why-we-have-banned-default-exports-and-you-should-do-the-same-d51fdc2cf2ad
Subjects: javascript
Tags: #javascript

*ES2015* was the most important improvement to Javascript in years. Among many great features it brought brand new module system — *Ecma Script Modules* which finally solved the problem of sharing code between files (modules) on a language level. It was a huge step forward but it needed to work with already existing modules, especially CommonJS used by node (*require*). That’s why it required some compromises, one of them is the existence of default exports. In this article, I want to explain why here at [Neufund](https://neufund.org/), we decided to ban default exports and use solely named ones.

![Why%20we%20have%20banned%20default%20exports%20in%20Javascript%20a%20c4e38e0723d54b62a73a5025ad8b5b9f/1ktJUMJO60oHoluiEV6KBmA.png](Why%20we%20have%20banned%20default%20exports%20in%20Javascript%20a%20c4e38e0723d54b62a73a5025ad8b5b9f/1ktJUMJO60oHoluiEV6KBmA.png)

ESM are part of ES6 (ES2015)

## Better DX

Default exports don’t export any name ie. symbol that can be easily associated with a exported value. Named exports, on the other hand, are all about having a name (pretty obvious right 😂) . This makes possible for IDEs to find and import dependencies for you automatically, which is a huge productivity boost.

![Why%20we%20have%20banned%20default%20exports%20in%20Javascript%20a%20c4e38e0723d54b62a73a5025ad8b5b9f/1k7kSQJw1Pb-8DkyEhE-BHw.gif](Why%20we%20have%20banned%20default%20exports%20in%20Javascript%20a%20c4e38e0723d54b62a73a5025ad8b5b9f/1k7kSQJw1Pb-8DkyEhE-BHw.gif)

How cool is that?

## Refactoring

Default exports make large-scale refactoring impossible since each importing site can name default import differently (including typos).

```
// in file exports.js
export default function () {...}// in file import1.js
import doSomething from "./exports.js"// in file import2.js
import doSmth from "./exports.js"
```

On the contrary, named exports make such refactoring trivial.

## Better tree shaking

Sometimes you can be tempted to export one huge object with many properties as default export. This is an anti-pattern and prohibits proper tree shaking:

```
// do not try this at home
export default {
  propertyA: "A",
  propertyB: "B",
}// do this instead
export const propertyA = "A";
export const propertyB = "B";
```

Using named exports can reduce your bundle size when you don’t use all exported values (especially useful while building libs).

## Summary

Default exports were introduced mostly for easier interoperability with thousands CommonJS modules that were exporting single values like:

```
module.exports = function() {...}
```

They don’t bring many benefits when used internally in your codebase, so please avoid them and thus make world a better place 😁

*For more news, tips & tricks about Typescript, Javascript and blockchain, follow me on [Twitter](https://twitter.com/krzKaczor)!*

***Krzysztof Kaczor** is the Lead Blockchain Developer at*

*[Neufund](https://medium.com/u/d428f4a0af6e?source=post_page-----d51fdc2cf2ad--------------------------------)*

*, a community-owned fundraising platform bridging the worlds of equity investments and blockchain.*

*To learn more, check out* *[Neufund’s ICBM page](https://commit.neufund.org/)* *or* *[the whitepaper](https://neufund.org/whitepaper)* *for a deep dive. Questions? Ask us anything on* *[Twitter](https://twitter.com/neufundorg)* *and* *[Telegram](https://t.me/neufund)!*