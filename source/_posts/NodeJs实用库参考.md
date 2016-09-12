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

## Camel Case

驼峰命名格式化工具

- 安装

npm install camel-case --save

- 使用

```
var camelCase = require('camel-case')

camelCase('string')         //=> "string"
camelCase('dot.case')       //=> "dotCase"
camelCase('PascalCase')     //=> "pascalCase"
camelCase('version 1.2.10') //=> "version_1_2_10"

camelCase('STRING 1.2', 'tr') //=> "strıng_1_2"

camelCase('string 1 2 3', null, true) //=> "string123"
```

## No Case

转换字符串为小写并以空格分割格式化工具

- 安装

npm install no-case --save

- 使用

```
var noCase = require('no-case')

noCase(null)              //=> ""
noCase('string')          //=> "string"
noCase('dot.case')        //=> "dot case"
noCase('camelCase')       //=> "camel case"
noCase('Beyoncé Knowles') //=> "beyoncé knowles"

noCase('A STRING', 'tr') //=> "a strıng"

noCase('HELLO WORLD!', null, '_') //=> "hello_world"
```

## node-readme

采用一个ES6的模板，自动生成README.md文件

- 安装

npm install --save-dev node-readme

- 使用
在package.json文件中为你的模块增加一个命令：然后可以用 npm run readme 命令来生成README.md文件

```
"scripts": {
  "readme": "node ./node_modules/.bin/node-readme"
},
```
[详细模板](https://github.com/revolunet/node-readme/blob/master/src/.README.md)
