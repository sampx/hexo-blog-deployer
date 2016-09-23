---
title: Hexo源码分析
date: “2016-09-22 17:29:23”
categories: 技术笔记
tags:
- Nodejs
---
## 从hexo-cli开始
（hexo-cli的安装请自行查阅网站）
hexo命令从usr/local/bin目录加载，调用../lib
/node_modules/hexo-cli/bin/hexo,再调用../lib/hexo.js
<!-- more -->
## 主程序模块
入口entry:
1.获取命令cmd和参数args，并对args用minimist进行处理。
2.加载了context.js（见"context模块"节）
3.加载了logger，在context中定义
4.定义了错误处理方法
5.findPkg从当前目录或者--cwd指定的工作目录，一直向上级目录查找package.json,直到找到含有此文件，并且是合法的hexo站点的目录路径后返回，如果没找到hexo模块则执行第8步
6.设置hexo的根目录为base_dir
7.加载hexo模块(如果hexo不是全局安装的，将无法加载，因此需要在hexo站点目录执行，或者使用--cwd参数指定站点目录)，若主模块加载成功，将new一个Hexo对象并返回（见“hexo主模块”节）
8.执行console模块（lib/console/index.js），如果获取到hexo模块则传入，否则只是传入当前的上下文（context模块），将help,init,version模块注册到extend.console
9.执行hexo.init(),如果没有找到hexo主模块，则context中init是一个空方法，估计是只为了触发init事件，便于插件在此时挂载，否则执行hexo主模块中的init方法。

## context模块
入口构造函数：
1.传入命令路径cmd和参数args
2.将自身注册为事件发射器（EventEmitter）
3.初始化logger
4.加载了Console Extend模块（extend/console.js），放在extend属性中。（见"extend/console模块"节）
5.定义call方法，通过名字获取extend中的插件，并调用。

## extend/console模块

console模块有两个属性：
- store 存放插件function对象
- alias 存放以插件名称缩写的

定义register方法，用来注册插件，插件都包装成Promise

## hexo主模块
构造函数：
1.获取路径和参数，注意所有的文件目录路径加载都是基于你执行目录或用--cwd制定的目录，正常应该是你网站生成器的主目录。
2.将自己注册为事件发射器
3.定义资源目录：public、source、node_modules、scripts、scaffolds、themes以及themes下面的scripts目录
4.将各类调试以及生成参数（包括启动hexo时的参数），定义到env属性中
5.加载配置文件，默认是基目录下的_config.yml，也可以从--config参数自行制定
6.加载扩展
  - console： 命令注册中心，见“extend/console模块“节
  - deployer: 部署插件注册中心
  - filter：过滤器注册中心，有两种filter：before_post_render,after_post_render，同时定义了解除注册、执行和同步执行方法
  - generator：生成器注册中心
  - helper：帮助插件注册中心
  - migrator：迁移插件注册中心
  - processor：处理器注册中心
  - renderer：视图渲染器注册中心
  - tag
7.设置config、log、render、route、post、scaffold、database
