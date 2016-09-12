---
title: NodeJs实用库参考
categories: 技术参考
tags:
  - NodeJs
date: 2016-09-12 20:43:45
---
-----
## minimist

解析命令行参数和选项

1. 创建一个新目录

mkdir test && cd test

2. 用npm初始化一个package.json

npm init //按引导回答一系列提示

3. 安装minimist包

npm install minimist --save

4. 新建minimist文件夹并创建一个新文件parse.js，写入以下代码：

```js
var argv = require('minimist')(process.argv.slice(2));
console.dir(argv);
```

5. 执行程序

```
node minimist/parse.js -a beep -b boop
//结果为：两个选项被解释成了map，使用非常方便
{ _: [], a: 'beep', b: 'boop' }
```
再来一个稍微复杂点的例子：

```
node minimist/parse.js -x 3 -y 4 -n5 -abc --beep=boop foo bar
 baz
//结果为：一目了然，使用很方便
{ _: [ 'foo', 'bar', 'baz' ],
  x: 3,
  y: 4,
  n: 5,
  a: true,
  b: true,
  c: true,
  beep: 'boop' }
```
选项说明：
> * opts.string : a string or array of strings argument names to always treat as strings
> * opts.boolean : a boolean, string or array of strings to always treat as booleans. if true will treat all double hyphenated arguments without equal signs as boolean (e.g. affects --foo, not -f or --foo=bar)
> * opts.alias : an object mapping string names to strings or arrays of string argument names to use as aliases
> * opts.default : an object mapping string argument names to default values
> * opts.stopEarly : when true, populate argv._ with everything after the first non-option
> * opts['--'] : when true, populate argv._ with everything before the -- and  argv['--'] with everything after the --. Here's an example:

```
require(''minimist'')('one two three -- four five --six'.split(' '), { '--': true })
{ _: [ 'one', 'two', 'three' ],
  '--': [ 'four', 'five', '--six' ] }
```

Note that with opts['--'] set, parsing for arguments still stops after the --.

> * opts.unknown - a function which is invoked with a command line parameter not defined in the opts configuration object. If the function returns false, the unknown option is not added to argv.
