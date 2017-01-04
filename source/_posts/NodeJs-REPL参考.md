---
title: NodeJs-REPL参考
date: "2017-01-04 20:43:45"
categories: 技术参考
tags:
- Nodejs
comments: true
---
REPL表示读取评估和演示打印循环(Read Eval Print Loop)，它代表一个命令输入和系统在交互模式的输出响应窗口控制台或Unix/ Linux的shell计算机环境。 Node.js附带了一个REPL环境。它执行以下期望的任务。
<!-- more -->
Node REPL结合Node.js的代码试验非常有用，用于调试JavaScript代码。

- Read - 读取用户的输入，解析在内存中输入JavaScript数据结构和存储。
- Eval - 接受和评估计算数据结构
- Print - 打印结果
- Loop - 循环上面的命令，直到用户按Ctrl-C两次。

## 启动REPL

```
Usage: node [options] [ -e script | script.js ] [arguments] 2 node debug script.js [arguments]
 Options:
  -v, --version
  -e, --eval script     直接执行javascript代码
  -p, --print           打印 --eval 的结果
  --v8-options          打印V8命令选项
  --max-stack-size=val  设置V8栈大小(bytes)
```

## 多行表达式
Node REPL支持类似于JavaScript的多行表达。让我们来看看以下do-while循环的操作：
```
$ node
> var x = 0
undefined
> do {
... x++;
... console.log("x: " + x);
... } while ( x < 5 );
x: 1
x: 2
x: 3
x: 4
x: 5
undefined
>
```

## 下划线变量
您可以使用下划线_得到最后的结果：
```
$ node
> var x = 10
undefined
> var y = 20
undefined
> x + y
30
> var sum = _
undefined
> console.log(sum)
30
undefined
>
```
## REPL命令

- ctrl + c - 终止当前命令
- ctrl + c twice - 终止Node REPL
- ctrl + d - 终止Node REPL
- Up/Down Keys - 查看命令历史记录和修改以前的命令
- tab Keys - 当前指令的列表
- .help - 所有命令的列表
- .break - 退出多行表达式
- .clear - 退出多行表达式
- .exit - 退出repl
- .save filename - 当前Node REPL会话保存到文件中
- .load filename - 加载文件的内容在当前Node REPL会话

## 注意
一门语言在运行的时候，需要一个环境，叫做宿主环境。对于JavaScript，宿主环境最常见的是web浏览器，这时的this通常指代的时window。而在node的REPL中，this指代的是global。
