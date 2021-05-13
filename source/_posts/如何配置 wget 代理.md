---
title: 如何配置 wget 代理
date: 2021-04-19 16:55:00
tags: 
   - 代理
categories: 
- [工具, proxy]
---

在开了全局代理的情况下，wget 与 git 一样，也需要再配置代理

<!--more-->
## 方法一:参数设置
```shell
wget -e http_proxy=127.0.0.1:1089 google.com



--2020-03-01 11:53:55--  http://google.com/

Connecting to 127.0.0.1:1089... connected.

Proxy request sent, awaiting response... 301 Moved Permanently

Location: http://www.google.com/ [following]

--2020-03-01 11:53:57--  http://www.google.com/

Reusing existing connection to 127.0.0.1:1089.

Proxy request sent, awaiting response... 200 OK

Length: unspecified [text/html]

Saving to: ‘index.html’



index.html                               [ <=>                                                                 ]  12.56K  --.-KB/s    in 0s



2020-03-01 11:54:01 (160 MB/s) - ‘index.html’ saved [12863]
```

## 方法二:配置文件设置
进入目录
> cd ~

创建.wgetrc配置文件
> n vim .wgetrc

设置代理
```
http_proxy = http://your_proxy:port

https_proxy = http://your_proxy:port

proxy_user = user

proxy_password = password

use_proxy = on

wait = 15
```