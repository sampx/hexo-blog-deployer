---
title: Linux常用命令参考
date: 2016-09-08 17:29:23
categories: 技术笔记
tags:
- Linux
---
记录Linux常用命令及用法，随便写写，加深记忆，自用备查。

## 一、文件压缩和打包

### 1. gzip/gunzip
```sh
	[root]# gzip filename.ext
	[root]# ls -l
	filename.ext.gz
	[root]# gunzip filename.ext.gz
```

### 2. tar
```shell
	[root]# tar -czvf boot.tgz /boot
	[root]# tar -zxvf boot.tgz -C /tmp
```
### 3. bizp2
```shell
	bzip2 intall.log
	ls insatll.log.bz2
	insatll.log.bz2
	bzip2 -d insatll.log.bz2
```
### 4. cpio

 -  备份
```shell
	[root]# find /etc -name *.conf | cpio -cov > /tmp/conf.cpio
```
 - 还原
```shell
	[root]# cpio --absolute-filenames -icvu < /tmp/conf.cpio
```

## 二、文件系统

### 1. 分区
```
    fdisk -l
```
### 2. 磁盘挂载
```
    mount DEVICE MOUNT_POINT
    mkdir newDisk
    mount /dev/sdb1 newDisk
    mount  --显示所有挂载
```
### 3. 解除挂载
```
    umount DEVICE/PATH
    or
    umount MOUNT_POINT
```
### 4. 查看可用空间
```shell
    df -h
```
### 5. 设置启动自动挂载： /etc/fstab
```shell
    echo "/dev/sdb1 /root/newDisk ext3 defaults 0 0" >> /etc/fstab
```
此命令将/dev/sdb1挂载到/root/newDisk目录，defaults表示默认挂载，第一个0代表dump时此分区不存档，第二个0表示系统启动时不对此分区进行fsck（1给根分区用，2检查完根分区后检查）

### 6. 磁盘检查：fsck
磁盘错误可以用fsck尝试修复：（**磁盘必须是未挂载状态，否则会造成文件系统损坏**）
```shell
    fsck -t TYPE /DEVICE/PATH
    fsck -t ext3 /dev/sdb1
```
## 三、逻辑卷

### 1. 概念

 - 物理卷（PV），物理分区
 - 卷组（VG），PV的集合
 - 逻辑卷（LV），PV中划出来的一块逻辑磁盘

### 2. 制作逻辑卷

1.将磁盘分区创建为物理卷（**分区格式必须是8e**）
```
    fdisk /dev/sdb
    pvcreate /dev/sdb1
    pvcreate /dev/sdb2
    ...
```
2.查看物理卷信息
```shell
    pvscan or pvdisplay
```
3.创建并查询卷组
```shell
    vgcreate VG_NAME DEVICE1 ... DEVICEn //创建
    vgscan or vgdisplay        //查询所有
```
4.扩容卷组
```shell
    vgextend VG_NAME DEVICE1 ... DEVICEn
```
5.创建逻辑卷
```shell
    lvcreate -L SIZE -n LV_NAME VG_NAME
    lvdisplay
```
6.创建文件系统并挂载
```shell
    mkfs ext3 LV_NAME
    mkdir MOUNT_POINT
    mount DEVICE MOUNT_POINT
```
## 四、文件链接

### 1. 硬链接

Linux中所有的文件都会有一个编号，称为inode，多个文件名指向同一索引节点是被允许的，这种链接叫硬链接，当最后一个链接被删除时，文件的数据块才被释放。
硬链接有两个限制：

 * 不允许给目录创建硬链接
 * 只有在同一文件系统内的文件之间才能创建硬链接，即不同分区上的两个文件之间不能建立硬链接
```shell
    ln TARGET_FILE LINK_NAME
    ls -li     //查看inode信息（链接数加一）
```

### 2. 软链接
软链接称为符号链接，是一个包含了另一个文件路径的文件，可以指向任意文件或目录，也可以跨不同的文件系统。

```shell
    ln -s TARGET_FILE LINK_NAME
    ls -li     //查看inode信息（链接数不变）
```

## 五、实用命令

### grep 搜索文本
`grep -ivnc 'express' filename `
* `-i` 不区分大小写
* `-c` 统计包含匹配的行数
* `-n` 输出行号
* `-v` 反向匹配

### gpasswd 添加用户到组

```
gpasswd -a demo sudo //将用户加入sudo组
```

### Ubuntu 启用root
启用root用户或密码重设：
sudo passwd root

临时切换命令如下：
sudo -s -H

关闭root帐户
sudo passwd -l root

### 安装telnet
sudo apt-get install xinetd telnetd


## 六、实用工具
### ufw防火墙
```bash
sudo apt-get install ufw
sudo ufw allow ssh
sudo ufw allow 4444/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw status
sudo ufw -h
sudo ufw show added
sudo ufw enable
```
[进一步学习](https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server)

### 设置NTP时间同步服务

```
sudo dpkg-reconfigure tzdata //配置时区

sudo apt-get update
sudo apt-get install ntp //安装ntp服务

```
### nc测试udp端口
```
nc -vuz 42.11.12.13 123
Connection to 42.11.12.13 123 port [udp/ntp] succeeded!
```
nc命令用法：

```
usage: nc [-46DdhklnrStUuvzC] [-i interval] [-p source_port]
[-s source_ip_address] [-T ToS] [-w timeout] [-X proxy_version]
[-x proxy_address[:port]] [hostname] [port[s]]
```
