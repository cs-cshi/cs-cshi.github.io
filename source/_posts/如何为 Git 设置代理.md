---
title: 如何为 Git 设置代理
date: 2021-03-13 09:47:57
tags: 代理
categories: 
- [工具, proxy]
---

## 1. 连接情况总览

如果在克隆或从远程仓库获取数据时遇到很慢甚至超时的情况，那么此时可能需要配置 Git 的代理。这里讲讲两种情况的代理方法：使用 HTTP 或 HTTPS 协议连接到 Git 仓库的代理方法和使用 SSH 协议连接到 Git 仓库的代理方法。
<!--more-->
- 如果远程仓库的格式像下面那样，这种就是使用 HTTP 或 HTTPS 协议连接到 Git 仓库的情况
```
http://github.com/cms-sw/cmssw.git
https://github.com/cms-sw/cmssw.git
```

- 如果远程仓库的格式像下面那样，这种就是使用 SSH 协议连接到 Git 仓库的情况
```
git@github.com:cms-sw/cmssw.git
ssh://git@github.com/cms-sw/cmssw.git
```

## 2. 使用 HTTP 或 HTTPS 协议连接到 Git 仓库的代理方法
### 2.1 针对所有域名的 Git 仓库
```
# HTTP/HTTPS 协议，port 需与代理软件设置的一致
git config –global http.proxy http://127.0.0.1:port

# SOCKS5 协议
git config --global http.proxy socks5://127.0.0.1:port
```
注意：
- `--glboal` 选项指的是修改 Git 的全局配置文件`~/.gitconfig`，而非各个 Git 仓库里的配置文件`.git/config`。
- `port`则为端口号。

### 2.2 针对特定域名的 Git 仓库
```
# HTTP/HTTPS 协议
git config –global http.url.proxy http://127.0.0.1:port
# 以 Github 为例
git config –global http.https://github.com.proxy http://127.0.0.1:port

# SOCKS5 协议
git config –global http.url.proxy socks5://127.0.0.1:port
# 以 Github 为例
git config –global http.https://github.com.proxy socks5://127.0.0.1:port
```
注意：
- `url` 即为需要走代理的仓库域名，url 以 http:// 和 https:// 打头的均用这个方法。
- 网上很多中文教程，可能会告诉你`https://`打头的 url 使用“git config –global https.https://example.com.proxy protocol://127.0.0.1:port”，这种做法其实是错的！记住一点：Git 不认`https.proxy`，设置`http.proxy`就可以支持 https 了。
- 如果想了解 url 的更多模式，如子域名等的情况，可参照 Git 的官方文档 。网页内容搜索 http.<url>.*，即可找到相关信息。

## 3. 使用 SSH 协议连接到 Git 仓库的代理方法
Git 依靠 ssh 处理连接时，为了通过代理进行连接，必须配置 ssh 本身，在 ~/.ssh/config 文件中设置 ProxyCommand 选项。Linux 和 macOS 是通过 nc 来执行 ProxyCommand 的，Windows 下则是通过 connect。

### 3.1 Linux 和 macOS 用户
编辑 `~/.ssh/config` 文件,给文件加上如下对应内容
```
# HTTP 代理
Host github.com
    User git
    ProxyCommand nc -X connect -x 127.0.0.1:7890 %h %p
```
解释:
- Host 后面 接的 `github.com` 是指定要走代理的仓库域名。
- 在 ProxyCommand 中，Linux 和 macOS 用户用的是 nc。
- -X 选项后面接的是 connect 的意思是 HTTPS 代理。
- -x 选项后面加上代理地址和端口号。
- 在调用 ProxyCommand 时，％h 和 ％p 将会被自动替换为目标主机名和 SSH 命令指定的端口（%h 和 %p 不要修改，保留原样即可）。

```
# SOCKS5 协议
# 两种方式任选一个

# 第一种
Host github.com
    User git
    ProxyCommand nc -X 5 -x 127.0.0.1:7891 %h %p
    
# 第二种
Host github.com
    User git
    ProxyCommand nc -x 127.0.0.1:7891 %h %p
```
解释:
- Host 后面 接的 github.com 是指定要走代理的仓库域名。
- 在 ProxyCommand 中，Linux 和 macOS 用户用的是 nc 。
- 在调用 ProxyCommand 时，％h 和 ％p 将会被自动替换为目标主机名和 SSH 命令指定的端口（ %h 和 %p 不要修改，保留原样即可）。
- 如果 -X 选项后面接的是数字 5，那么指的就是 socks5 代理。
- 当然不写上 -X 选项也是可以的，因为在没有指定协议的情况下，默认是使用socks5代理的。所以2 种的写法效果一样 ，都指的是走 socks5 代理

###  3.2 Windows 用户
编辑 `~/.ssh/config` 文件,给文件加上如下对应内容.windows 的 ~ 路径一般是`C:\Users\用户名`,可在 git bash 中 输入 `cd ~`进入 ~目录,再用`pwd`命令显示当前路径.
```
# HTTP代理
Host github.com
    User git
    ProxyCommand connect -H 127.0.0.1:7890 %h %p
    
# SOCKS5代理
Host github.com
    User git
    ProxyCommand connect -S 127.0.0.1:7891 %h %p
```
解释:
- Host 后面 接的 github.com 是指定要走代理的仓库域名。
- 在 ProxyCommand 中，Windows 用户用的是 connect。
- -H 选项的意思是 HTTP 代理
- -S 选项指的就是 socks5 代理
- 在调用 ProxyCommand 时，％h 和 ％p 将会被自动替换为目标主机名和 SSH 命令指定的端口（ %h 和 %p 不要修改，保留原样即可）。

## 4. 署名
本文内容遵循 署名-非商业性使用-相同方式共享 4.0 国际 (CC BY-NC-SA 4.0) 协议
参考文章永久链接是：https://ericclose.github.io/git-proxy-config.html
本文根据参考文章仅做了少量修改