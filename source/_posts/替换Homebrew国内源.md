---
title: 替换Homebrew国内源
date: '2017-01-05 20:45:31'
categories: 实用技巧
tags:
  Mac
comments: true
keywords: Mac,Homebrew
description: Homebrew是OS X上类似APT(apt-get)、Yum的一个软件包管理器。 所以，虽然你侥幸下载到了brew，但你肯定是无法更新brew的。替换Homebrew国内镜像源，加速Homebrew更新。
---
Homebrew是OS X上类似APT(apt-get)、Yum的一个软件包管理器。 所以，虽然你侥幸下载到了brew，但你肯定是无法更新brew的。原因你懂。不过虽然不能更新brew，但这只是指brew这个管理器本身罢了，软件还是可以安装的。但由于无法使用brew update也就无法更新...
** 注意：本方法适合1.1.6版的Homebrew，老版本要用另外的办法（文中有介绍）
<!-- more -->
## 检查home brew的版本
```
brew -v
Homebrew 1.1.6
```
如果版本低于上述版本，请参阅：[这篇文章](https://yq.aliyun.com/articles/5055?do=login)
但是，步骤参照上文，仍然是没法成功clone的，因为国内源的地址已经改了，所以对应的源地址要更改为：
清华大学镜像源：
https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git
科大镜像源：
https://mirrors.ustc.edu.cn/homebrew-core.git

如果版本是1.1.6及以上，请按照以下办法更新源：
```
cd /usr/local/Homebrew
//以下换成清华的源也可以
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
brew update //等待成功喜讯 ：）
```

**enjoy！**
