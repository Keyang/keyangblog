title: 'minimist: a neat cli argv parser'
date: 2016-03-16 17:41:36
tags:
  - npm
  - node.js

---
[minimist](https://github.com/substack/minimist) is a lightweight cli argument (command line arguments) parser:

```js
require('minimist')("-x 3 -y 4 -n5 -abc --beep=boop foo bar baz");

//output:
{ _: [ 'foo', 'bar', 'baz' ],
  x: 3,
  y: 4,
  n: 5,
  a: true,
  b: true,
  c: true,
  beep: 'boop' }

```
