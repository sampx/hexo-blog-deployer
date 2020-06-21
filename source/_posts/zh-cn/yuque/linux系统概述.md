---
title: linux系统概述
urlname: ai30w2
date: 2020-06-21 15:27:26 +0800
tags: [linux]
categories: [linux]
---

## linux 架构层次

![](https://raw.githubusercontent.com/sampx/resources/master/images/linux_note/99E0F8A8231CEA62B59C55D866A81DE4.jpg#align=left&display=inline&height=835&margin=%5Bobject%20Object%5D&originHeight=835&originWidth=1024&status=done&style=none&width=1024)

- 用户模式：

用户程序在用户模式下运行，访问内存和 cpu 有一定限制，用户程序崩溃，不会导致整个系统崩溃，但用户程序也有可能带来严重影响，比如清除硬盘数据，当然这取决于权限的控制。

- 内核模式

内核中的程序运行在内核模式下，内核模式下代码可以不受限制的访问 cpu 和内存，这种模式非常强大但也非常危险，程序出错可以轻易的使整个系统崩溃。

- 主内存（也称作内核空间）

内核和进程在主内存中运行，cpu 像一个操作员一样处理内存中的数据，读取指令和数据，然后计算后将结果写回内存。内核将主内存划分为很多区块，并且维护这些区块的状态，每一个进程拥有自己的内存区块，内核必须确保进程只使用它自己的内存区块。

- 用户空间

用户空间是指所有用户进程占用的所有内存空间，一个进程简单说就是内存中的一个状态。

## 用户

一个用户代表一个实体，它有权限运行用户进程，对文件拥有所有权。
内核是通过用户 ID 来管理用户的，用户 ID 是一串数字标识。
用户机制主要用于权限管理。每一个用户进程都有一个用户作为所有者，我们称其为以该用户运行的进程。
root 用户虽然权限很高，但是还是在用户模式而非内核模式中运行。

## 内核

内核负责管理进程、内存、设备驱动程序和系统调用。

![](https://raw.githubusercontent.com/sampx/resources/master/images/linux_note/53425978C75AF929C50ABBD3141ADEB9.jpg#align=left&display=inline&height=643&margin=%5Bobject%20Object%5D&originHeight=768&originWidth=1024&status=done&style=none&width=857)

## 进程管理

- CPU 为每个进程计时，到时即停止进程，并切换至内核模式，由内核接管 CPU 控制权。
- 内核记录下当前 CPU 和内存的状态信息，这些信息在恢复被停止的进程时需要用到。
- 内核执行上一个时间段内的任务（如从输入输出设备获得数据，磁盘读写操作等）。
- 内核准备执行下一个进程，从准备就绪的进程中选择一个执行。
- 内核为新进程准备 CPU 和内存。
- 内核将新进程执行的时间段通知 CPU。
- 内核将 CPU 切换至用户模式，将 CPU 控制权移交给新进程。

> 注解：内核是在上下文切换时的时间段间隙中运行，在多 CPU 系统中，如果新进程将在另一个 CPU 上运行，内核就不需要让出当前 CPU 的使用权。不过为了将所有 CPU 的使用效率最大化，内核会使用一些其他的方式来获取 CPU 控制权

## 内存管理

- 内核在上下文切换过程中管理内存；
- 内核需要自己的专有内存空间，其他的用户进程无法访问；
- 每个用户进程有自己的专有内存空间；
- 一个进程不能访问另一个进程的专有内存空间；
- 用户进程之间可以共享内存；
- 用户进程的某些内存空间可以是只读的；
- 通过使用磁盘交换，系统可以使用比实际内存容量更多的内存空间。

> 注解：内存地址映射通过内存页面表（page table）来实现。
> 新型的 CPU 提供了 MMU（Memory Management Unit，内存管理单元），MMU 使用了一种叫作虚拟内存的内存访问机制。

## 设备驱动程序和设备管理

- 通常设备只能在内核模式中被访问，例如用户进程请求内核关闭系统电源
- 不同设备之间没有一个统一的编程接口，比如两个不同的网卡。
- 所以设备驱动程序传统意义上来说是内核的一部分，它们尽可能为用户进程提供统一的接口。

## 系统调用和系统支持

- 打开、读取和写文件这些操作都涉及系统调用
- 两个非常重要的系统调用：fork()和 exec()
- 除了 init 以外，Linux 中的所有用户进程都是通过 fork()来启动的。

> fork()：当进程调用 fork()时，内核创建一个和该进程几乎一模一样的副本。
> exec()：当进程调用 exec(program)时，内核启动 program 来替换当前的进程。

- 新进程的启动流程

![](https://raw.githubusercontent.com/sampx/resources/master/images/linux_note/BA65AA0FC77C9F12126EE68676DE59CC.jpg#align=left&display=inline&height=370&margin=%5Bobject%20Object%5D&originHeight=370&originWidth=526&status=done&style=none&width=526)

当你在终端窗口中输入 ls 时，终端窗口中的 shell 调用 fork()创建一个 shell 的副本，然后该副本调用 exec(ls)来运行 ls。

