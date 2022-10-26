# react-spring

Created: May 7, 2022 11:04 AM
Finished: No
Source: https://react-spring.io/
Tags: #react

![react-spring%20261337c8c6494fafbaa53019c4a8dc13/tg1mN1F.gif](react-spring%20261337c8c6494fafbaa53019c4a8dc13/tg1mN1F.gif)

*These demos are real, click them!*

**react-spring** is a spring-physics based animation library that should cover most of your UI related animation needs. It gives you tools flexible enough to confidently cast your ideas into moving interfaces.

This library represents a modern approach to animation. It is very much inspired by Christopher Chedeau's [animated](https://github.com/animatedjs/animated) and Cheng Lou's [react-motion](https://github.com/chenglou/react-motion). It inherits animated's powerful interpolations and performance, as well as react-motion's ease of use. But while animated is mostly imperative and react-motion mostly declarative, react-spring bridges both. You will be surprised how easy static data is cast into motion with small, explicit utility functions that don't necessarily affect how you form your views.

To install the entire `react-spring` library:

react-spring is cross platform, it supports the web, react-native, react-native-web and practically any other platform, we have specifically exported the following targets (these are available to install as opposed to the entire module to fit your use case):

The library comes as an es-module compiled for evergreen browsers with the following browserlist config: `[>1%, not dead, not ie 11, not op_mini all](https://browserl.ist/?q=%3E1%25%2C+not+dead%2C+not+ie+11%2C+not+op_mini+all)`. If you need to support legacy targets or deal with targets that don't support modules, you can use the commonJS export by simply appending `.cjs` to your imports.

The principle you will be working with is called a `spring`, it *does not have a defined curve or a set duration*. In that it differs greatly from the animation you are probably used to. We think of animation in terms of time and curves, but that in itself causes most of the struggle we face when trying to make elements on the screen move naturally, because nothing in the real world moves like that.

![react-spring%20261337c8c6494fafbaa53019c4a8dc13/7CCH51r.gif](react-spring%20261337c8c6494fafbaa53019c4a8dc13/7CCH51r.gif)

We are so used to time-based animation that we believe that struggle is normal, dealing with arbitrary curves, easings, time waterfalls, not to mention getting this all in sync. As Andy Matuschak (ex Apple UI-Kit developer) [expressed it once](https://twitter.com/andy_matuschak/status/566736015188963328): *Animation APIs parameterized by duration and curve are fundamentally opposed to continuous, fluid interactivity*.

Springs change that, animation becomes easy and approachable, everything you do looks and feels natural by default. For a detailed explanation watch the video below:

[react-spring%20261337c8c6494fafbaa53019c4a8dc13/FloGbmnXSe3Gd49LAWXQ](react-spring%20261337c8c6494fafbaa53019c4a8dc13/FloGbmnXSe3Gd49LAWXQ)

[react-spring%20261337c8c6494fafbaa53019c4a8dc13/8165984](react-spring%20261337c8c6494fafbaa53019c4a8dc13/8165984)

If you like this project, please consider helping out. All contributions are welcome as well as donations to [Opencollective](https://opencollective.com/react-spring), or in crypto: 36fuguTPxGCNnYZSRdgdh6Ea94brCAjMbH (BTC) or 0x6E3f79Ea1d0dcedeb33D3fC6c34d2B1f156F2682 (ETH).

You can also support this project by becoming a sponsor. Your logo will show up here with a link to your website.

Thank you to all our backers! 🙏

![react-spring%20261337c8c6494fafbaa53019c4a8dc13/backers.svg](react-spring%20261337c8c6494fafbaa53019c4a8dc13/backers.svg)

This project exists thanks to all the people who contribute.

![react-spring%20261337c8c6494fafbaa53019c4a8dc13/contributors.svg](react-spring%20261337c8c6494fafbaa53019c4a8dc13/contributors.svg)