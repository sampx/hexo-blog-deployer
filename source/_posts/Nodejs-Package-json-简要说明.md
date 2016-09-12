---
title: Nodejs Package.json 简要说明
date: 2016-09-09 23:30:38
tags: Nodejs
---
---
*TODO：以后会区分出不同的Node版本支持的配置参数*

Package.json文件描述了一个NPM包的所有相关信息，包括作者、简介、包依赖、构建等信息。格式必须是严格的JSON格式。

通常我们在创建一个NPM程序时，可以使用`npm init`命令，通过交互式的命令，自动生成一个package.json文件，里面包含了常用的一些字段信息，但远不止这么简单。通过完善package.json文件，我们可以让npm命令更好地为我们服务。下面简要介绍包定义文件中各部分的作用和含义：

- name & version
name和version是package.json中最重要的两个字段，也是发布到NPM平台上的唯一标识，如果没有正确设置这两个字段，包就不能发布和被下载。
**name字段：**新版本的NPM可以指定scope, 名字可以加前缀标识，如`@ijse/mypackage`
**version字段：**取值需要符合node-semver的规则，[详见文档](https://github.com/isaacs/node-semver)
下面是官方给出的一些建议:

> * 名字里不要再包含"js"和"node"，因为默认NPM包就是node.js程序，不过你可以通过engines字段来指定。
> * 名字将会被作为url的一部分，所有要符合http url的一般命名规则，不能包含url非法字符，也不能以.和_开头。
> * 名字也将作为require()命令的参数，所以应该尽量简明。
> * 如果包要发布到NPM平台上的话，最好先检查下有没有重名, 并且字母只能全部小写。

- description
包的描述信息，将会在`npm search`的返回结果中显示，以帮助用户选择合适的包。
- keywords
包的关键词信息，是一个字符串数组，同上也将显示在`npm search`的结果中。
- homepage：包的主页地址
- license：包的开源协议名称
- author：包的作者，可以是字符串或对象，如：
```json
  author: {  
    "name": "ijse",
    "email": "my@ijse.cn",
    "url": "http://www.ijser.cn"
  }
```
或者
```json
  author: "ijse <my@ijser.cn> (http://www.ijser.cn)"  
```
- bugs：包的bug跟踪主页地址，应该如下设置：
```json
  bugs: {  
    "url": "http://github.com/ijse/project/issues",
    "email": "my@ijser.cn"
  }
```
- contributors & maintainers：包的贡献者和维护人，是一个数组。
- files：包所包含的所有文件，可以取值为文件夹。通常我们还是用`.npmignore`来去除不想包含到包里的文件。
- main：包的入口文件，如index.js
- bin：如果你的包里包含可执行文件，通过设置这个字段可以将它们包含到系统的PATH中，这样直接就可以运行，很方便。当包被安装后，NPM将创建一个cli.js文件的链接到/usr/local/bin/iapp下。如：
```json
  "bin": {
    "iapp": "./cli.js"
  }
```
- man：为系统的man命令提供帮助文档, 帮助文件的文件名必须以数字结尾，如果是压缩的，需要以.gz结尾。如：
```json
  "man": "./man/doc.1"
  //如果是字符串数组，则分别可以man foo, man foo-bar, man 2 foo来查看。：
  "name": "foo",
  "man": ["./man/foo.1", "./man/bar.1", "./man/foo.2" ]
```
- directories：CommonJS包所要求的目录结构信息，目前除了告诉别人你的程序目录结构，貌似没有别的什么用。 下级字段可以是：`lib, bin, man, doc, example`。 每个都是字符串。
- repository：包的仓库地址。如：
```json
  "repository": {
    "type": "git",
    "url": "http://github.com/ijse/project.git"
  }
```
- scripts & config：通过设置这个可以使NPM调用一些命令脚本，封装一些功能。config可以添加一些设置，供scripts读取用，同时这里的值也会被添加到系统的环境变量中。
```json
  //npm start的时候会读取到npm_package_config_port环境变量。
  "name": "foo",
  "config": {
    "port": "8080"
  }
```
同时也可以使用npm config命令来修改设置：
```json
  npm config set foo:port 8001  
```
- dependencies：指定依赖的其它包，这些依赖是指包发布后正常执行时所需要的，通常使用下面命令来安装：
```sh
  npm install --save otherpackage  
```
引用包的版本形式可以有如下多种：
> * version 严格匹配某个版本
> * `>version` 必须大于某个版本
> * `>=version`
> * `<version`
> * `<=version`
> * `~version` 大概匹配某个版本
> * `^version` 兼容某个版本
> * 1.2.x 可以是1.2.0, 1.2.1等等，但不能是1.3.0
> * http://... 指定tarball的url地址
> * `*` 任何版本都可以
> * `""` 同上
> * `version1 - version2`：说明 `>=version1 <=version2`
> * `range1 || range2`: 满足range1 或range2
> * `git://...` git地址
> * `user/repo` 同上
> * `tag` 指定某个tag的版本
> * `path/path` 本地包所有文件夹

例如，以下都是可用的：
```json
  { "dependencies" :
    { "foo" : "1.0.0 - 2.9999.9999"
    , "bar" : ">=1.0.2 <2.1.2"
    , "baz" : ">1.0.2 <=2.3.4"
    , "boo" : "2.0.1"
    , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
    , "asd" : "http://asdf.com/asdf.tar.gz"
    , "til" : "~1.2"
    , "elf" : "~1.2.3"
    , "two" : "2.x"
    , "thr" : "3.3.x"
    , "lat" : "latest"
    , "dyl" : "file:../dyl"
    }
  }
```
Git URL可以有如下种形式：
```
  git://github.com/user/project.git#commit-ish  
  git+ssh://user@hostname:project.git#commit-ish  
  git+ssh://user@hostname/project.git#commit-ish  
  git+http://user@hostname/project/blah.git#commit-ish  
  git+https://user@hostname/project/blah.git#commit-ish
```
- devDependencies：如果是开发中依赖的包，可以在devDependencies设置。语法同上。还可以通过命令行来添加：
```sh
  npm install --save-dev mypack
```
- peerDependencies：相关的依赖，如果你的包是插件，而用户在使用你的包时候，通常也会需要这些依赖（插件），那么可以将依赖列到这里。举个例子，如karma, 这些都是karma的相关插件，一般使用karma的时候都会需要。它的package.json中有设置：
```json
  "peerDependencies": {
    "karma-jasmine": "~0.1.0",
    "karma-requirejs": "~0.2.0",
    "karma-coffee-preprocessor": "~0.1.0",
    "karma-html2js-preprocessor": "~0.1.0",
    "karma-chrome-launcher": "~0.1.0",
    "karma-firefox-launcher": "~0.1.0",
    "karma-phantomjs-launcher": "~0.1.0",
    "karma-script-launcher": "~0.1.0"
  }
```
- bundledDependencies：绑定的依赖包，发布的时候这些绑定包也会被一同发布。
- optionalDependencies：即使这些依赖没有，也可以正常安装使用。
- engines：指定包运行的环境，如：//engineStrict设置为true强制限定 engine
```json
  "engines": {
    "node": ">=0.10.3 < 0.12",
    "npm": "~1.0.20"
  }
```
- os：指定你的包可以在哪些系统平台下运行。如下例，该包即可以在darwin和linux平台下运行，而不能在win32下(取值来自`process.platform`)。还可以指定包运行的cpu架构（取值来自`process.arch`），如：
```json
  "os": [ "darwin", "linux", "!win32" ]
  "cpu": [ "x64", "!arm" ]
```
- preferGlobal：如果你的包是命令行运行的，那可以将其设置为true建议用户全局(npm install -g)安装。但它并不强制用户。
- private：设为true这个包将不会发布到NPM平台下。
- publishConfig：这个字段用于设置发布时候的一些设定。尤其方便你希望发布前设定指定的tag或registry。也可以设定其它子字段，但只有tag和registry会影响到发布。

**NPM的一些默认值说明**
* `"scripts": { "start": "node server.js" } `
//如果在项目根目录下含有server.js文件，则NPM会自动设置此值。
* `"scripts": { "preinstall": "node-gyp rebuild" }`
//如果在项目根目录下含有binding.gyp文件，则NPM会自动设置此值。
* `"contributors": [...]`
//如果项目根目录下含有AUTHORS文件，则NPM会自动将每一行以Name <email> (url)的格式读取并设定此字段。
