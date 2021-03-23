---
title: 解决 AMD 在 ubuntu 上无法外接显示器问题
date: 2021-03-15 09:58:57
tags: 
   - linux
categories: linux
---

实际上 ubuntu 的内核（5.4及以下）还不支持 AMD 4000 系列的集显，所以无法驱动。

解决办法是更新 ubuntu 内核版本

<!--more-->

更新前最好用 timeshift 备份系统

matebook14 2020 锐龙版 4600H CPU 实测 v5.7 内核能支持。

v5.7：[](https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.7/)https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.7/

升级方法：
1. 下载内核包(共四个)
   第一个：linux-headers_***all.deb
   其余三个：分三类（headers、module、image）全选择 generic 或者 lowlatency 下载。
2. `sudo dpkg -i `依次安装
   我的安装顺序：all -> headers -> module -> image。
3. 重启电脑，输入命令`uname -a` 查看是否升级成功 

**参考**

- [联想小新Pro13 2020锐龙版R7 4800U安装ubuntu-20.04](https://zhuanlan.zhihu.com/p/149564937)