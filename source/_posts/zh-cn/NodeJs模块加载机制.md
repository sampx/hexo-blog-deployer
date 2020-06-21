---
title: NodeJs模块加载机制
date: 2017-01-04 20:43:45
categories: 技术参考
tags:
- Nodejs
comments: true
---
Node.js的模块分为两类，一类为原生（核心）模块，一类为文件模块。
原生模块在Node.js源代码编译的时候编译进了二进制执行文件，被定义在lib这个目录下面，加载的速度最快。
文件模块是动态加载的，加载速度比原生模块慢。但是Node.js对原生模块和文件模块都进行了缓存，于是在第二次require时，是不会有重复开销的。

## 文件模块加载
由于通过命令行加载启动的文件几乎都为文件模块。我们从Node.js如何加载文件模块开始谈起。
文件模块的加载，主要由原生模块module来实现和完成，该模块在启动时已经被加载。

文件模块中，又分为3类模块。这三类文件模块以后缀来区分，Node.js会根据后缀名来决定加载方法。
- .js。通过fs模块同步读取js文件并编译执行。
- .node。通过C/C++进行编写的Addon。通过dlopen方法进行加载。
- .json。读取文件，调用JSON.parse解析加载。

在此，重点讲js模块加载过程：
- 模块包装
对js文件内容进行头尾包装。以app.js为例，包装之后的app.js将会变成以下形式：
```
(function (exports, require, module, __filename, __dirname) {
    var circle = require('./circle.js');
    console.log('The area of a circle of radius 4 is ' + circle.area(4));
});
```
这段代码执行后，返回为一个具体的function对象。最后传入module对象的exports，require方法，module，文件名，目录名作为实参并执行。这就是为什么require并没有定义在app.js 文件中，但是这个方法却存在的原因。

- module对象
  - module.children：一个数组，指定了当前模块引用的其他模块。
  - module.exports ：此对象由Module系统创建，有时不可访问; 如果希望模块成为一个类的实例，只需将所需的导出对象分配给module.exports。
  - exports：最初指向module.exports，如果你给它赋值，它将会指向新对象，而不是最初的module.exports。 注意，如果将所需的对象分配给exports，将简单地重新绑定exports变量，这可能不是你想要做的。
  类似于：
  ```
  function require(...) {
  // ...
  ((module, exports) => {
    // Your module code here
    exports = some_func;        // re-assigns exports, exports is no longer
                                // a shortcut, and nothing is exported.
    module.exports = some_func; // makes your module export 0
  })(module, module.exports);
  return module;
  }
  ```
  - module.filename：模块的绝对路径名称
  - module.id ：模块id通常等于module.filename
  - module.loaded：用于判断模块是否加载完毕，或者正在加载中。
  - module.parent ：指向首次加载本模块的模块。
  - module.require(id)：返回一个module.exports导出的对象。

载入、编译、缓存了module后，返回module的exports对象。这就是circle.js文件中只有定义在exports对象上的方法才能被外部调用的原因。

- require方法中的文件查找策略

![enter image description here](http://www.infoq.com/resource/articles/nodejs-module-mechanism/zh/resources/image1.jpg)
尽管原生模块与文件模块的优先级不同，但是都不会优先于从文件模块的缓存中加载已经存在的模块。以http模块为例，尽管在目录下存在一个http/http.js/http.node/http.json文件，require(“http”)都不会从这些文件中加载，而是从原生模块中加载。

文件查找流程：
![enter image description here](http://www.infoq.com/resource/articles/nodejs-module-mechanism/zh/resources/image2.jpg)

简而言之，如果require绝对路径的文件，查找时不会去遍历每一个node_modules目录，其速度最快。其余流程如下：

1. 从module path数组中取出第一个目录作为查找基准。
2. 直接从目录中查找该文件，如果存在，则结束查找。如果不存在，则进行下一条查找。
3. 尝试添加.js、.json、.node后缀后查找，如果存在文件，则结束查找。如果不存在，则进行下一条。
4. 尝试将require的参数作为一个包来进行查找，读取目录下的package.json文件，取得main参数指定的文件。
5. 尝试查找该文件，如果存在，则结束查找。如果不存在，则进行第3条查找。
6. 如果继续失败，则取出module path数组中的下一个目录作为基准查找，循环第1至5个步骤。
7. 如果继续失败，循环第1至6个步骤，直到module path中的最后一个值。
8. 如果仍然失败，则抛出异常。

整个查找过程十分类似原型链的查找和作用域的查找。所幸Node.js对路径查找实现了缓存机制，否则由于每次判断路径都是同步阻塞式进行，会导致严重的性能消耗。

## 包结构
一个符合CommonJS规范的包应该是如下这种结构：

- 一个package.json文件应该存在于包顶级目录下
- 二进制文件应该包含在bin目录下。
- JavaScript代码应该包含在lib目录下。
- 文档应该在doc目录下。
- 单元测试应该在test目录下。

由上文的require的查找过程可以知道，Node.js在没有找到目标文件时，会将当前目录当作一个包来尝试加载，所以在package.json文件中最重要的一个字段就是main。而实际上，这一处是Node.js的扩展，标准定义中并不包含此字段，对于require，只需要main属性即可。详细Package-json包说明参见：
[这篇blog](http://blog.udock.cn/2016/09/09/Nodejs-Package-json-%E7%AE%80%E8%A6%81%E8%AF%B4%E6%98%8E/#more)
