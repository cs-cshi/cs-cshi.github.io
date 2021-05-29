<!-- ---
title: 被动信息收集工具
date: 2021-05-25 20:20:57
tags: 
      - 网络安全
      - 渗透
categories: 
- [网络安全, 渗透]
--- -->

被动信息收集工具介绍

<!--more-->
> 1. 公开渠道可获得的信息
> 2. 与目标系统不产生直接交互
> 3. 尽量避免留下一切痕迹

美国军方：
1. [Army Intelligence and Security Doctrine](https://fas.org/irp/doddir/army/)
2. [ATP 2-22.9. Open-Source Intelligence ](https://fas.org/irp/doddir/army/atp2-22-9-2017.pdf)

北大西洋公约组织：
1. [Open Source Intelligence]:http://information-retrieval.info/docs/NATO-OSINT.html

## 0. 信息收集内容
- ip 地址段
- 域名信息
- 邮件地址
- 文档图片数据
- 公司地址
- 公司组织架构
- 联系号码/传真号码
- 人员姓名/职务
- 目标系统使用的技术架构
- 公开的商业信息

## 1. DNS
- 域名与 FQDN(全限定域名) 的区别
  - 域名：baidu.com
  - FQDN：www.baidu.com
- 域名记录
  - A：主机记录，记录主机域名，解析为主机IP
  - C name：别名记录，把域名解析为另一个域名
  - NS：域名服务器记录，用来指定该域名由哪个DNS服务器来进行解析。
  - MX：邮件交换记录，指向这个域的SMTP服务器地址
  - ptr：反向解析，把IP解析为域名
![](dns流程.png)
> 递归查询 + 迭代查询

- nslookup
```shell
$ nslookup
> www.sina.com
Server   1.1.1.1  # 当前使用的域名服务器
Address  2.2.2.2  
```