---
title: 解决 virtualBox 桥接网卡未指定问题
date: 2021-05-22 20:05:00
tags: 
      - 工具
categories: 
- [工具, 虚拟机]
---

主要原因：window10 中没有安装virtualbox的桥接驱动

<!--more-->

## 1. 界面样例
![样例](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/virtualbox_%E7%95%8C%E9%9D%A2.png)

## 2. 问题原因
window10中没有安装virtualbox的桥接驱动

## 3. 解决方法
1. 进入到网络连接
  ![网络连接](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/virtualbox_%E7%BD%91%E7%BB%9C%E8%BF%9E%E6%8E%A5.png)
2. 右键打开属性
   ![属性](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/virtualbox_wlan%E5%B1%9E%E6%80%A7.png)
3. 没有启动，那么现在开始去安装桥接驱动
   点击 【安装】----->【服务】------>【添加】----->【从磁盘安装】----->【浏览】
4. 找到virtualbox目录中的一个文件【VBoxNetLwf】
   ![driver](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/virtualbox_driver_file.png)
5. 找到文件后开始安装
   ![安装](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/virtualbox_driver_install.png)
6. 安装完成后重新打开 virualbox 网络界面，可以看到网卡驱动已经可以加载了

————————————————
本文转载自[virtualBox 桥接网卡 未指定](https://blog.csdn.net/qq_383698639/article/details/79527311)