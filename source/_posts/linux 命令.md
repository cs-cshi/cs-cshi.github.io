---
title: linux 命令
date: 2021-05-21 10:00:00
tags: 
      - linux
categories: 
- [linux]
---

记录 linux bash 命令

<!--more-->
## 0. 终端命令说明
- command [-options] [parameter]
  - command：命令名, 比如: ls、pwd
  - [-options]：选项，可以有零个、一个或者多个选项，多个选项可以合并，比如使用的 -r 就是选项。
  - [parameter]：参数，可以有零个、一个 或者 多个参数， 比如: touch 文件名、mkdir 目录名、cd 目标目录(路径)，这些文件名和目录名都是参数。
  - []：代表可选
  - 命令的选项和参数一般情况下没有顺序要求，但是scp命令是必须先选项然后在跟上参数。

- 查看命令帮助
  - --help : command --help
  - man : man command
  | 操作键 | 说明 |
  | --- | --- |
  | 空格 | 显示下一屏信息 |
  | 回车 | 显示下一行信息 |
  | b	| 显示上一屏信息 |
  | f	| 显示下一屏信息 | 
  | q | 退出 |

## 1. 查看目录命令
| 命令 | 说明 |
|---|---|
| ls | 查看当前目录信息 |
| ls -l | 以列表方式显示 |
| ls -h | 显示文件大小单位，默认是字节 |
| ls -a | 显示隐藏文件和隐藏目录 |
| tree | 以树状方式显示目录信息 |
| pwd | 查看当前目录路径 |
| clear | 清除终端内容 |

ls -l 详情
![ls参数详情](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/linux/ls%E9%80%89%E9%A1%B9%E8%AF%A6%E6%83%85.png)

## 2. 切换目录命令 cd 
| 命令 | 说明 |
|---|---|
| cd 目录 |	切换到指定目录 |
| cd ~	| 切换到当前用户的主目录 |
| cd .. |	切换到上一级目录 |
| cd .	| 切换到当前目录 |
| cd -	| 切换到上一次目录 |

- 绝对路径
  - 从 / 目录开始
- 相对路径
  - 从当前目录开始

## 3. 创建、删除、复制、移动
| 命令 | 说明 |
|---|---|
| touch 文件名	| 创建指定文件 |
| mkdir 目录名	| 创建目录(文件夹) |
| mkdir -p | 创建所依赖的文件夹/递归创建文件夹 |
| rm 文件名	| 删除指定文件 |
| rm -i | 交互式提示 |
| rm -r | 递归删除目录及其内容 |
| rm -f |	强制删除，忽略不存在的文件，无需提示 |
| rm -d | 删除空目录 |
| rm -r 目录名	| 删除指定目录 |
| rmdir 目录名	| 删除空目录 |
| cp	| 拷贝文件、拷贝目录 |
| cp -i | 交互式提示 |
| cp -r | 递归拷贝目录及其内容 |
| cp -v | 显示拷贝后的路径描述 |
| cp -a | 保留文件的原有权限 |
| mv	| 移动文件、移动目录、重命名 |
| mv -i | 交互式提示 |
| mv -v | 显示移动后的路径描述 |

## 4. 重定向
| 命令 | 说明 |
|---|---|
| >	| 如果文件存在会覆盖原有文件内容，相当于文件操作中的‘w’模式 |
| >> | 如果文件存在会追加写入文件末尾，相当于文件操作中的‘a’ 模式 |

## 5. 查看文件内容
| 命令 | 说明 |
|---|---|
| cat | 查看小型文件 |
| more | 分屏查看大型文件 |
| less | 分屏查看大型文件, 比 more 少百分比 |
| tail | 默认显示文件最后10行内容 |
| watch | 根据指定间隔时间重复运行后续命令 |
> - cat命令结合重定向可以完成多个文件的合并
> - tail -n
>   - n 为要查看的内容的行数
> - `watch -n 2 tail -20 /var/log/messages`
>   - 每个两秒查看 messages 文件最后20行

more 操作说明：
| 操作键 | 说明 |
| --- | --- |
| 空格 | 显示下一屏信息 |
| 回车 | 显示下一行信息 |
| b | 显示上一屏信息 |
| f | 显示下一屏信息 |
| q | 退出 |

## 6. 管道命令 | 
管道(|)：一个命令的输出可以通过管道做为另一个命令的输入，可以理解成是一个容器，存放在终端显示的内容。

## 7. 链接命令 ln
| 命令 | 说明 |
|---|---|
| ln -s | 创建软链接 |
| ln | 创建硬链接 |
> - 软链接: 类似于Windows下的快捷方式。
>   - ln -s 源文件路径(使用绝对路径) 软链接
>   - 如果软链接和源文件不在同一个目录，源文件要使用绝对路径，不能使用相对路径。
>   - 删除源文件则软链接失效
>   - 可以给目录创建软链接
> - 硬链接: 类似于源文件的一个别名，也就是说这两个名字指向的是同一个文件数据。
>   - 创建硬链接使用相对路径和绝对路径都可以
>   - 删除源文件，硬链接还可以访问到数据。
>   - 创建硬链接，硬链接数会加1，删除源文件或者硬链接，硬链接数会减1。
>   - 创建软链接，硬链接数不会加1
>   - 不能给目录创建硬链接
> - 硬链接数就是文件数据被文件名使用的次数, 好比引用计数

## 8. 文本处理命令 
| 命令 | 说明 |
|---|---|
| grep | 文本搜索 |
| grep -i | 忽略大小写 |
| grep -n | 显示匹配行号 |
| grep -v | 显示不包含匹配文本的所有行 |
| egrep  | 在文件内查找指定的字符串。|
| awk | 文本数据处理工具 |
> - grep 搜索内容 文本
>   - grep 'a' test.txt
>   - ls | grep test
> - egrep执行效果与 grep-E 相似
>   - egrep是用extended regular expression语法来解读的，而grep则用basic regular expression 语法解读，extended regular expression比basic regular expression的表达更规范。 
> - awk 命令也是逐行扫描文件（从第 1 行到最后一行），寻找含有目标文本的行，如果匹配成功，则会在该行上执行用户想要的操作；反之，则不对行做任何处理。
>   - netstat -pantu | egrep -v '0.0.0.0|:::' | awk '{print $5}'，打印 netstat -pantu 的第五列，以空格作分隔符

**grep 可以结合正则表达式一起使用**
| 正则表达式 | 说明 |
| --- | --- |
| ^ | 以指定字符串开头 |
| $ | 以指定字符串结尾 |
| . | 匹配一个非换行符的字符 |

## 9. 查找文件命令
> whereis 命令比 find 快，原因：非源码安装的软件信息实际上会存储在 linux 的一个数据库中， whereis 是通过查询数据库获得信息

| 命令 | 说明 |
|---|---|
| find | 在指定目录下查找文件(包括目录) |
| find -name | 根据文件名(包括目录名)字查找 |
| which | 查看命令位置 |

**find命令结合通配符的使用**
通配符不仅能结合 find 命令使用，还可以结合其它命令使用, 比如: ls、mv、cp 等，只有 find 命令使用通配符需要加上引号。
| 通配符 | 说明 |
| --- | --- |
| * | 代表0个或多个任意字符 |
| ? | 代表任意一个字符 |
> find -name "1?1.txt"

## 10. 压缩和解压缩命令tar
- Linux默认支持的压缩格式:
  - .gz
  - .bz2
  - .zip
> .gz和.bz2的压缩包需要使用tar命令来压缩和解压缩
> .zip的压缩包需要使用zip命令来压缩，使用unzip命令来解压缩

| 命令 | 说明 |
|---|---|
| tar | 压缩和解压缩命令 |

| 参数 | 说明 |
|---|---|
| -c | 创建打包文件 |
| -v | 显示打包或者解包的详细信息 |
| -f | 指定文件名称, 必须放到所有选项后面 |
| -z | 压缩或解压缩(.gz) |
| -j | 压缩或解压缩(.bz2) |
| -x | 解包 |
| -C | 解压缩到指定目录 |

| 命令 | 说明 |
|---|---|
| zip | 压缩成.zip格式文件 |
| unzip | 解压缩.zip格式文件 |
| unzip -d | 解压缩到指定目录 |

## 11. 权限命令
### 1. 文件权限
> - 字母法格式: chmod 不同角色设置的权限 文件
> - 数字法格式: chmod 不同角色的权限值 文件名

| 命令 | 说明 |
|---|---|
| chmod | 修改文件权限 |

| 角色 | 说明 |
|---|---|
| u | user, 表示该文件的所有者 |
| g | group, 表示用户组 |
| o | other, 表示其他用户 |
| a | all, 表示所有用户 |

| 操作符 | 说明 |
|---|---|
| +	| 增加权限 |
| -	| 撤销权限 |
| = | 设置权限 |

| 权限 | 说明 |
|---|---|
| r | 可读 |
| w | 可写 |
| x | 可执行 |
| - | 无任何权限 |

**chmod 数字法的使用**

| 权限 | 说明 |
| --- | --- |
| r | 可读，权限值是4 |
| w | 可写，权限值是2 |
| x | 可执行，权限值是1 |
| - | 无任何权限，权限值是0 |

### 2. 管理员权限
| 命令 | 说明 |
|---|---|
| sudo -s	| 切换到root用户，获取管理员权限|
| sudo | 某个命令的执行需要获取管理员权限可以在执行命令前面加上sudo |
| whoami | 查看当前用户限 |
| exit | 退出登录用户 |
> - exit 
>   - 如果是切换后的登陆用户，退出则返回上一个登陆账号。
>   - 如果是终端界面，退出当前终端。

## 12. 用户相关操作
> /etc/passwd 文件记录了所有用户信息

### 1. 创建用户
| 命令 | 说明 |
| --- | --- |
| useradd | 创建(添加)用户 |
| id | 查看用户信息 |
> > 创建用户 chang 并指定用户组 test : sudo useradd -m -g test chang

| 参数 | 说明 |
| --- | --- |
| -m | 自动创建用户主目录,主目录的名字就是用户名 |
| -g | 指定用户所属的用户组，默认不指定会自动创建一个同名的用户组 |

- 说明
  - useradd 命令的使用需要使用管理员权限，前面需要加上 sudo
  - 创建用户如果不指定用户组，默认会自动创建一个同名的用户组
  - 查看用户是否创建成功，可以查看/etc/passwd这个文件
  - 查看用户组是否创建成功，可以查看/etc/group这个文件
- passwd文件中的每项信息说明,以root:x:0:0:root:/root:/bin/bash为例:
  - 第一个：用户名
  - 第二个：密码占位符
  - 第三个：uid, 用户id
  - 第四个：gid, 用户所在组id
  - 第五个：用户描述, 可选，
  - 第六个：用户的主目录所在位置
  - 第七个：用户所用 shell 的类型，一般由bash或者sh，默认不设置是sh类型
- group文件中的每项信息说明, 以chang:x:1001:为例:
  - 第一个：用户组名
  - 第二个：用户组密码占位符，一般Linux系统的用户组都没有密码的
  - 第三个：组id

### 2. 设置密码
sudo passwd 用户名

### 3. 切换用户
| 命令 | 说明 |
| --- | --- |
| su | 切换用户 |

### 4. 修改用户信息
| 命令 | 说明 |
| --- | --- |
| usermod | 修改用户信息 |

| 参数 | 说明 |
| --- | --- |
| -G | 设置一个附加组 |
| -g | 修改用户组 |

### 5. 删除附加组
| 命令 | 说明 |
| --- | --- |
| gpasswd | 添加和删除附加组信息 |

| 参数 | 说明 |
| --- | --- |
| -a 用户名	| 给用户添加附加组 |
| -d 用户名	| 给用户删除附加组 |

### 6. 删除用户
| 命令 | 说明 |
| --- | --- |
| userdel | 删除用户 |

| 参数 | 说明 |
| --- | --- |
| -r 用户名 | 删除用户主目录，必须要设置，否则用户主目录不会删除 |
> 删除用户，默认同名的用户组也会被删除

## 13. 用户组相关操作
### 1. 创建用户组
| 命令 | 说明 |
| --- | --- |
| groupadd | 创建(添加)用户组 |
> 创建用户 chang 并指定用户组 test : sudo useradd -m -g test chang

### 2. 修改用户组
| 命令 | 说明 |
| --- | --- |
| usermod | 创修改用户的用户组 |
> sudo usermod -g test1 chang

### 3. 删除用户组
| 命令 | 说明 |
| --- | --- |
| groupdel | 删除用户组 |
> 如果用户组下面有用户先删除用户在删除用户组

## 14. 远程登录、远程拷贝命令
| 命令 | 说明 |
| --- | --- |
| ssh | 远程登录 |
| scp | 远程拷贝 |

### 1. ssh
在Ubuntu安装ssh客户端命令: 
```shell
sudo apt-get install openssh-client
```
若Ubuntu作为服务端,需要安装ssh服务端软件.
```shell
sudo apt-get install openssh-server
```

ssh 连接命令：ssh 用户名@ip地址
```shell
ssh chang@172.16.47.197
```

### 2. scp
scp是基于ssh进行远程文件拷贝的命令，需要保证服务端和客户端电脑安装了相应的ssh软件

- scp命令格式:
  - 远程拷贝文件
    - scp 本地文件 远程服务器用户名@远程服务器ip地址:指定拷贝到远程服务器的路径
    - scp 远程服务器用户名@远程服务器ip地址:远程服务器文件 指定拷贝到本地电脑的路径
  - 远程拷贝目标
    - scp -r 本地目录 远程服务器用户名@远程服务器ip地址:指定拷贝到远程服务器的路径
    - scp -r 远程服务器用户名@远程服务器ip地址:远程服务器目录 指定拷贝到本地电脑的路径
    - -r 表示递归拷贝整个目录
> 大量的文件上传和下载可以通过可视化工具FileZilla来完成。

## 14. 软件安装与卸载
### 1. 软件安装
Ubuntu软件安装有两种方式:
- 离线安装(deb文件格式安装）
- 在线安装(apt-get方式安装)

dpkg 是 Ubuntu 的安装包格式，可以使用 dpkg 命令进行软件的安装和卸载
| 命令 | 说明 |
| --- | --- |
| dpkg | 安装和卸载deb安装包 |
| dpkg -i | 离线安装deb安装包 |

apt-get 是在线安装 deb 软件包的命令，主要用于在线从互联网的软件仓库中搜索、安装、升级、卸载软件。
> sudo apt–get install 安装包

### 2. 软件卸载
Ubuntu软件卸载有两种方式:
- 离线安装包的卸载(deb 文件格式卸载）
- 在线安装包的卸载(apt-get 方式卸载)

deb 文件格式卸载
> sudo dpkg –r 安装包名

apt-get 方式卸载
> sudo apt-get remove 安装包名

## 15. 查看系统实时信息
| 命令 | 说明 |
| --- | --- |
| top | 查看系统实时信息 |
> k，结束某进程：pid to signal/kill
> q,退出

## 16. 查看进程信息
 ps 命令 为了适应不同的 UNIX 系统，可用格式非常多
- ps aux ：可以查看系统中所有的进程；
- ps -le ： 可以查看系统中所有的进程，而且还能看到进程的父进程的 PID 和进程优先级；
- ps -l ： 只能看到当前 Shell 产生的进程；

## 17. 网络相关命令
### 1. ifconfig 命令
| 命令 | 说明 |
| --- | --- |
| ifconfig | 查看或修改网络接口配置信息 |

| 参数 | 说明 |
| --- | --- |
| -a | 显示全部接口信息。|
| -s | 显示摘要信息（类似于 netstat -i）。|
| \<interface> address | 为网卡设置IPv4地址。|
| \<interface> add <address> | 给指定网卡配置IPv6地址。|
| \<interface> del <address> | 删除指定网卡的IPv6地址。|
| \<interface> netmask <address> | 设置网卡的子网掩码。掩码可以是有前缀0x的32位十六进制数，也可以是用点分开的4个十进制数。如果不打算将网络分成子网，可以不管这一选项；如果要使用子网，那么请记住，网络中每一个系统必须有相同子网掩码。 |
| \<interface> dstaddr <address> | 设定一个远端地址，建立点对点通信。|
| \<interface> tunnel <address> | 建立隧道。|
| \<interface> hw <address> | 设置硬件地址。|
| \<interface> mtu <NN> | 设置最大传输单元。 |
| \<interface> [-]arp | 设置指定网卡是否支持ARP协议。-表示不支持arp。|
| \<interface> multicast | 为网卡设置组播标志。|
| \<interface> [-]promisc | 设置是否支持网卡的promiscuous模式，如果选择此参数，网卡将接收网络中发给它所有的数据包。-表示关闭混杂模式。|
| \<interface> txqueuelen <NN> | 为网卡设置传输列队的长度。|
| \<interface> up | 启动指定网卡。|
| \<interface> down | 关闭指定网卡。该参数可以有效地阻止通过指定接口的IP信息流，如果想永久地关闭一个接口，我们还需要从核心路由表中将该接口的路由信息全部删除。|

### 2. macchanger
修改 mac 地址
> 只能修改让别人看见的mac地址，实际真正的mac地址不能改变

| 参数 | 说明 |
| --- | --- |
| -h,  --help | 帮助信息 |
| -V,  --version | 输出版本信息并退出 |
| -s,  --show | 输出MAC地址并退出 |
| -e,  --ending | 不改变有关设备商信息的字节 |
| -a,  --another | 设置相同供应商的随机MAC地址 |
| -A | 设置任意类型的供应商MAC |
| -p,  --permanent | 重置为原始、永久的硬件MAC |
| -r,  --random | 设置完全随机MAC地址 |
| -l,  --list[=keyword] | 输出已知的供应商 |
| -b,  --bia | 冒充地址 |
| -m,  --mac XX:XX:XX:XX:XX:XX | 设置 MAC XX:XX:XX:XX:XX:XX |

### 3. netstat
netstat命令用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况。netstat是在内核中访问网络及相关信息的程序，它能提供TCP连接，TCP和UDP监听，进程内存管理的相关报告。

 查看 Active Internet connections (servers and established)：
 > sudo netstat -pantu



 
————————————————

 - [ifconfig 命令详解](https://blog.csdn.net/u011857683/article/details/83758503)
 - [Macchanger-更改mac地址](https://blog.csdn.net/qq_26090065/article/details/80500137)
 - [Linux ps命令详解：查看正在运行的进程](http://c.biancheng.net/view/1062.html)
 - [Linux学习教程，Linux入门教程（超详细）](http://c.biancheng.net/linux_tutorial/)