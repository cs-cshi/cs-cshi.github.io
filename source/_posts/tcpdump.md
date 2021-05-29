---
title: tcpdump
date: 2021-05-25 14:00:00
tags: 
      - 网络安全
      - 工具
categories: 
- [网络安全, 工具]
---
 
 tcpdump 使用介绍

<!--more-->
> - wireshark 是图形化的分析工具，无法在 linux、unix 纯字符页面工作
> - wireshark 需要另外下载安装，且稍显笨重
> - tcpdump 是 No-GUI 的抓包分析工具，linux、unix 系统默认安装

## 1. 抓包
- 抓包
  - 默认只抓 68 个字节（通常能抓完包头）
  - tcpdump -i eth0 -s 0 -w file.pcap
    - -i 指定网口
    - -s 抓的数据包大小，0表示全抓，有多大抓多大
    - -w 抓到的数据包保存到文件
  - 抓包筛选器
    - tcpdump -i eth0 port 22 
- 读取抓包文件 pcap
  - tcpdump -r file.pcap
    - -A 以 ascaii方式显示 pcap 文件更详细的信息：tcpdump -A -r file.pcap
    - -A 以 16进制方式显示 pcap 文件更详细的信息：tcpdump -X -r file.pcap

## 2. 显示筛选器
```shell
$ tcpdump -n -r http.cap | awk '{print $3}'| sort –u 
```
> -n 不对包里的 ip 地址作域名解析
> -r 读取文件
> awk '{print $3}' 筛选第三列内容,awk默认以空格作分隔符
> sort -u 剔除重复项

```shell
$ tcpdump -n src host 145.254.160.237 -r http.cap
$ tcpdump -n src host 145.254.160.237 -r http.cap
$ tcpdump -n port 53 -r http.cap
$ tcpdump -n tcp port 53 -r http.cap
$ tcpdump -n udp port 53 -r http.cap
$ tcpdump -n port 53 -r http.cap
$ tcpdump -nX port 80 -r http.cap
```
> - src host 显示指定源 ip 数据
> - dst host 查看指定目的 ip 数据
> - port 按端口号筛选
> - tcp port 按 tcp 端口号筛选
> - X  16进制 + ascaii 码显示，与 wireshark 中显示效果类似

## 3. 高级筛选
> 作更细粒度的筛选

![1](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/others/tcpdump_tcp_head.png)
> 红色部分为 tcp 的 flag，即标签位，表示当前包是什么类型，如 SYN、ACK
> TCP 三次握手后 ACK 和 PSH 都会置1，二进制：00011000，对应十进制 24
```shell
$ tcpdump -A -n 'tcp[13] = 24' -r http.pcap
```
- tcp[13]：第14个字节，对应标签位
- 24：ACK 和 PSH 置 1，表示 TCP 数据传输

