# roundrobin

Created: May 27, 2022 2:10 AM
Finished: No
Source: https://www.npmjs.com/package/roundrobin
Tags: #tool

![338e4905a2684ca96e08c7780fc68412.png](roundrobin%2002faa9b0968942c2bf5cfd41c6179fef/338e4905a2684ca96e08c7780fc68412.png)

# Round Robin

A simple round robin tournament match scheduler using the standard [scheduling algorithm](http://en.wikipedia.org/wiki/Round-robin_tournament#Scheduling_algorithm).

## Usage

Simply give the number of players (with an optional players array), and it will spit out the array of rounds necessary:

```
var robin = require('roundrobin');
robin(6);
[ [ [ 1, 6 ], [ 2, 5 ], [ 3, 4 ] ],
  [ [ 5, 1 ], [ 6, 4 ], [ 2, 3 ] ],
  [ [ 1, 4 ], [ 5, 3 ], [ 6, 2 ] ],
  [ [ 3, 1 ], [ 4, 2 ], [ 5, 6 ] ],
  [ [ 1, 2 ], [ 3, 6 ], [ 4, 5 ] ] ]

// or with names supplied
robin(6, ['clux', 'lockjaw', 'pibbz', 'xeno', 'e114', 'eclipse']);
[ [ [ 'clux', 'eclipse' ], [ 'lockjaw', 'e114' ], [ 'pibbz', 'xeno' ] ],
  [ [ 'e114', 'clux' ], [ 'eclipse', 'xeno' ], [ 'lockjaw', 'pibbz' ] ],
  [ [ 'clux', 'xeno' ], [ 'e114', 'pibbz' ], [ 'eclipse', 'lockjaw' ] ],
  [ [ 'pibbz', 'clux', ], [ 'xeno', 'lockjaw' ], [ 'e114', 'eclipse' ] ],
  [ [ 'clux', 'lockjaw' ], [ 'pibbz', 'eclipse' ], [ 'xeno', 'e114' ] ] ]
```

### Home/Away Matches

In version `2.0.0` or greater, the outputted order of the match arrays denote which player is "home" or "away":

```
[ 'away', 'home' ] // index 0 is away and index 1 is home
```

This can be used to indicate home/away in sports, white/black in chess, etc.

## Installation

Install from npm:

```
$ npm install roundrobin
```

## License

MIT-Licensed. See LICENSE file for details.