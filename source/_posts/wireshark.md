---
title: wireshark
date: 2021-05-24 14:00:00
tags: 
      - 网络安全
      - 工具
categories: 
- [网络安全, 工具]
---

抓包嗅探协议分析

<!--more-->

> wirkshark 官网：[https://www.wireshark.org/](https://www.wireshark.org/)

## 1. 基本使用
- 选择抓包网卡
  ![1](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/network_card.png)
- 混杂模式(promiscuous)
  - 不选混杂模式，抓的数据包是发给该网卡、该网卡发出的，以及广播或组播（多播）的包，但对于单播等只是经过网卡的数据包不抓取。一般默认会勾选。
  ![2](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/promiscuous.png)
- 抓包筛选器
  - wireshark 有默认的筛选规则供选择，也可以自己写
  ![3](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/capture_filter.png) 
- 文件保存
  - file -> save，可以保存抓取的数据包，一般选择兼容性最好的 pcap 格式，pcapng 是 wireshark  专有的格式。
- 界面调整
  - edit -> preferences
  - 对显示的包信息的列进行添加
  ![4](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/preferences_columns.png)
  - preferences -> protocols 会列出 wireshark 支持的所有协议

## 2. 筛选器
目的：过滤掉干扰的数据包

分类：抓包筛选器、显示筛选器
> 实时抓包时 filter 框是显示筛选器
 
流程：
  Network -> Capture Filters -> Capture Engine -> Display Filters

```shell
# 可以通过鼠标选择具体的包，再在具体的字段属性右击，将规则 apply as filter
# 筛选出 udp 以及 来源不是 192.168.1.118 的包
(udp) && !(ip.src == 192.168.1.118)

# 筛选出 udp 以及 来源和目的都不是 192.168.1.118 的包
(udp) && !(ip.addr == 192.168.1.118)
```

## 3. 常见协议包
数据包的层次结构：
  wireshark 中由上至下显示的是自底向上的网络层次（物理层->数据链路层->...->应用层）
![5](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/wireshark_protocol.png)

访问非默认端口时，wireshark 不会按照默认协议去解析数据包，可以在包上右键 decode as ，手动选择解析的协议

## 4. 数据流
在对应的包上右击
![6](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/follow_stream.png) 
 https ssl 传输时，公钥证书会以明文的方式从服务器传输到客户端，客户端再产生后续会话加密用的对称密钥，再用证书里的公钥对会话密钥加密传给服务器，完成加密密钥的交换。 

 ## 5. 信息统计
 > wireshark 信息统计基本上都位于 Statistics 菜单栏中
 > wireshark 官网中有常见的协议的例子文件 [https://wiki.wireshark.org/SampleCaptures/#Sample_Captures](https://wiki.wireshark.org/SampleCaptures/#Sample_Captures)
 
 - summary(cpature file properties)：捕捉文件摘要
 - protocol hierarchy：协议分布，各协议流量占比
 - conversations：网络会话连接
 - endpoint：以太网，IP, TCP或UDP节点数
 - packet lengths：包大小分布

## 6. 缺陷
- 对大型网络文件分析效率低
- 企业抓包部署方案
  - sniffer
  - cace/ riverbed
  - cascad pilot