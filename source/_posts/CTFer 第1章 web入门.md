
<!-- ---
title: CTFer：第1章 web入门
tags: 
      - 网络安全
      - Web 安全
categories: 
- [网络安全, CTF, 《从0到1：CTFer成长之路》]
- [网络安全, Web 安全]
--- -->

按照 CTF 线上赛中各种 Web 漏洞出现的频率、漏洞的复杂程序，将 Web 漏洞分为入门、进阶、拓展三个层次。本章从入门层次开始，介绍 Web 类题目中最常见的 3 类漏洞，即信息搜索、SQL 注入、任意文件读取漏洞。
不全是《从0到1：CTFer成长之路》一书中的内容，会将学习过程中遇到的其他内容归纳到相应知识点中。

<!--more-->

# 1. 信息搜索
   信息搜索是前期的必备工作，也是重中之重。信息搜索简单分为敏感目录、敏感备份文件、Banner 识别。

## 1.1 敏感目录泄露
  通过敏感目录泄露，往往能获得网站源代码和敏感 URL 地址，如后台地址。
  1. git 泄露，将`.git`文件夹部署到了外网环境，导致攻击者可以通过`.git`文件夹获得源码。工具：https://github.com/denny0223/scrabble
  2. SVN 泄露，将 SVN 隐藏文件暴露于外网环境,可以利用 `.svn/entries`或`wc.db`文件获取源码信息。工具：https://github.com/kost/dvcs-ripper
  3. HG 泄露。`.hg`文件夹，包含代码和分支修改记录等信息。工具：https://github.com/kost/dvcs-ripper
  
  推荐**开源目录扫描工具**：https://github.com/maurosoria/dirsearch。
  
  4. 经验总结
    CTF 线上赛往往有重定向的问题，如只要访问`.git`便会返回403，此时可以试探访问`.git/config`，如果有内容返回，说明存在`git`泄露风险。

## 1.2 敏感备份文件
  通过访问一些敏感的备份文件，往往能获得某一文件的源码，亦或整个网站目录。
  1. gedit 备份文件，以"~"后缀结尾。
  2. vim 备份文件，vim 意外退出时会生成备份文件，"*.swp"、"*.swo"、"*.swn"、“*.un.”等形式。
  3. 常规文件，如`robots.txt`、`readme.md`、`网站名.tar.gz`
  4. 经验总结
     CTF 线上比赛过程中，出题人往往会在线运维题目，有时会导致备份文件生成，所以比赛过程中可以编写实时监控脚本，对题目服务进行监控。

## 1.3 Banner 信息
  一个网站的 Banner 信息，是服务器对外显示的一些基础信息。往往通过这些基础信息，如网站是用 ThinkPHP 框架编写时，可以尝试 ThinkPHP 框架的漏洞。
  1. 自行搜集 CMS 指纹库
  2. 已有工具：Wappalyzer(浏览器插件)

# 2 SQL 注入
## 2.1 SQL 注入基础
  SQL 注入是开发者对用户输入的参数过滤不严格，将可能存在的攻击载荷拼接到 SQL 查询语句中，导致用户输入的数据能够影响预设查询功能的一种技术，通常将导致数据库信息泄露、篡改，甚至被删除。

  **安全设计原则：数据与代码分离**

  注入攻击的本质，是把用户输入的数据当作代码执行。

  注入攻击的两个条件：1. 用户能控制输入  2. 原本程序要执行的代码，拼接了用户输入的数据。

  **注入技巧的优先级： UNION注入 > 报错注入 > 布尔盲注 > 时间注入**

### 2.1.1 数字型注入
  可以从数字运算这个特征行为来判断该注入点是否是数字型注入，表现为输入点是否被引号包裹。如注入 "3-1" 返回的结果与"2"的结果一致。

### 2.1.2 UNION 注入
  把使用 UNION 语句将数据展示到页面上的注入办法，通常和数字型注入或字符型注入联合起来。如联合数字型注入，`1 UNION SELECT ...`，可以先简单 UNION SELECT 1,2,3... 来判断有几列。

  现在很多数据库都有默认带有 information_schma 数据库，整个数据库的库名、表明、字段名都可以从中查到，虽然这样方便了数据库信息的查询，但也方便了 SQL 注入的利用。

  ```
  # 查询本数据库的所有表名
  -1 union select 1,group_concat(table_name) from information_schema.tables where table_schema = database()
  ```

  ```
  # 通过获取的表名获取表中列名
  -1 union select 1,group_concat(column_name) from information_schema.columns where table_name = ***
  ```

  数字型注入中，可以输入特别大的数或 -1 这类明显可能无返回结果的值，这样服务器返回的就只有我们想要的信息。

### 2.1.3 字符型注入
  与数字型注入点相反，字符型注入输入点被引号包裹。
   
  判断方法：先尝试数学表达式，再尝试 1a,2a等特性的输入，最后通过 *'#来判断，如果是字符，*后的 ' 会先与 SQL 语句形成闭合，而 # 会注释掉 SQL 后面那个 '。

### 2.1.4 布尔盲注、时间盲注
  布尔盲注：`id=1' and '1`,在这个查询中，如果能确定 `id=1` 的查询结果，那么后面那个 1 就可以作为我们用来判断数据是否猜测正确的预测点。这种虽然我们看不到数据，但可以通过注入推测出数据的技术称为布尔盲注。
  
  时间盲注：有些情况下，页面回显内容完全一致，需要借助其他手段对 SQL 注入的执行结果进行判断，如通过服务器执行 SQL 语句所需的时间，通过 sleep()，利用 IF 条件函数或 AND、OR 函数的短路特性和 SQL 执行的时间判断 SQL 攻击的结果。

  在 MySQL 中，有一个 BENCHMARK(count expr) 函数，它用于测试函数性能，函数的执行结果，是将 expr 执行 count 次。(不同数据库中都有类似的函数)
  ```sql
  -1 UNION SELECT IF(SUBSTRING(current, 1, 1) = CHAR(119), BENCHMARK(5000000, ENCODE('MSG','by 5 seconds')), null) FROM (SELECT Database() as current) as tb1;
  ```
  这段 payload 判断库名的第一个字母是否为 CHAR(119)，即小写w。若为真，则通过 BENCHMARK() 函数造成较长延时；若为假，则该语句很快会执行完。攻击者遍历所有的字母，直到整个数据库名全部验证完成为止。
  - database()：the name of the database currently connected to
  - system_user()：the system user for the database
  - current_user()：the current user who is logged in to the database
  - last_insert_id：the transaction ID of the last insert operation on the database.
 

### 2.1.5 报错注入
  有时为了方便开发者调试，有的网站会开启错误调试信息，即数据库将语句执行后的报错信息输出，这种注入称为报错注入。

  在 MySQL 中， updatexml 函数在执行时，第二个参数应该为合法的 XPATH 路径，否则会在引发报错的同时将传入的参数进行输出。利用这个特性，将想要得到的信息传入 updataxml 函数的第二个参数。如 `https:127.0.0.1/sql3.php?id=1' or updatexml(1,concat(0x7e,(select pwd from wp_user),0x7e),1)%23`
  - `concat` 函数，用于将多个字符串连接为一个字符串。concat()函数是将其连成一个字符串，因此不会符合XPATH_string的格式，从而出现格式错误，爆出用户。
  - `updatexml` 函数，作用：改变文档中符合条件的节点的值
    > updatexml(XML_document, XPath_string, new_value);
    >  - XML_document, string，为XML文档对象的名称
    >  - XPath_string Xpath格式的字符串
    >  - new_value，替换查找到的符合条件的数据
  - `0x7e`，ASCII码,实为`~`,upadtexml()报错信息为特殊字符、字母及之后的内容,为了前面字母丢失,开头连接一个特殊字符~
  
  如果目标开启了多语句执行，可以通过分号`;`执行多条语句，任意修改数据库的结构和数据。

## 2.2 注入点
### 2.2.1 SELECT 注入
1. 注入点在 select_expr。如`SELECT ${_GET['id']}, content FROM wp_news`，此时首先可以采取时间盲注，另外更优的方法是用 AS 别名的方法。例如注入`(SELECT pwd FROM wp_user) AS title`
2. 注入点在 table_reference。如`SELECT title FROM ${ }`，采用 AS 别名，可注入`(SELECT pwd AS title FROM wp_user)`
3. 注入点在 WHERE 或 HAVING 后。这种情况与注入基础中介绍的一致，要先判断有无引号包裹。
4. 注入点在 GROUP BY 或 ORDER BY 后。当遇到不是 WHERE 后的注入点时，先尝试可以输入什么，判断出当前注入的位置。
5. 注入点在 LIMIT 后。

### 2.2.2 INSERT 注入
1. 注入点在 table_name ，可以通过注释掉注入点后面的语句，在数据库中插入自己的数据，如管理员。
2. 注入点在 VALUES,此时可先闭合单引号，然后另行插入一条记录。

### 2.2.3 UPDATE 注入
  与 SELECT 注入类似

### 2.2.4 DELETE 注入
  DELETE 注入大多在 WHERE 后。为了保证不会对正常数据造成干扰，通常使用 `and sleep(1)` 的方式保证 WHERE 执行结果返回 FALSE，与时间盲注类似。

## 2.3 注入与防御
### 2.3.1 字符替换
  1. 过滤了空格。将空格替换位`%09`
  2. 将 SELECT 替换成空。可以用嵌套的方式，如 SESELECTLECT。
  3. 大小写匹配。在 MySQL 中，关键字是不区分大小写的，但替换语句通常区分大小写，故可采用混写。
  4. 正则匹配。正则匹配关键字`\bselect\b`，可以用形如`/*!50000select/`方式绕过。
  5. 替换了单引号或双引号，忘了反斜扛。如`WHERE id='可控1' AND title = '可控2'`，可控点都进行了单引号、双引号替换，此时可用`\`转义了可控点1 后`'`,这样会与可控点2前的引号`'`匹配起来，再利用`OR`将可控点2前的语句都无效，可控点2可注入。即 `where id = 'a\' and title=' OR ***#`
  
### 2.3.2 逃逸引号
  注入的重点在于逃逸引号，而开发者常会将用户的输入全局做一次 `addslashes`,即在预定义字符('、"、\）前添加反斜扛。
  1. 编码解码
     开发者常用到形如 urldecode、base64_decode 的解码函数或自定义的加解密函数。当用户输入 addslashes 函数时，数据处于编码状态，引号无法被转义。
  2. 意料之外的输入点
     开发者在转义用户输入时遗漏了一些可控点，以php为例，如上传的文件名、http header、 $_SERVER('PHP_SELF')$
  3. 二次注入。如 SQL 插入用户名 `admin'or'1`，经转义变成`admin\'or\'1`,正常入库。但当这个用户名被再次使用时， get 的 SQL 语句就变成了 `WHERE username = 'admin' or '1'`
  4. 字符串截断。如输入`aaaa'`，自动转义为`aaaa\'`，但由于长度限制，被截取成了`aaaa\`，正好转义了 SQL 语句中预置的单引号。

## 2.4 注入的功效
1. 有写文件权限时，用 `INTO OUTFILE` 或者 `DUMPFILE`向 Web 目录写文件，或者写入包含漏洞文件。
2. 有读文件权限时，用 laad_file()函数读取往回走那源码和配置信息。
3. 提升权限。
4. 通过注入控制数据库查询出来的数据，控制如模板、缓存等文件内容来获取权限，或者删除、读取某些关键文件。
5. 可执行多语句时，控制整个数据库。
6. 直接执行系统命令。

## 2.5 小结
  由于 SQL 服务器在实现时不同，即使时相同的功能，也会有多种多样的实现方式。

  为了做出题目或更深入了解 SQL 注入原理，最关键的是根据不同的 SQL 服务器类型，查找相关资料，通过模糊测试（fuzz testing）得出被过滤的字符、函数、关键词等，在文档中查找功能相同但不包含过滤特征的替代品。

  sqli-labs（https://github.com/Audi-1/sqli-labs）提供不同过滤等级下的注入题目。

# 3 任意文件读取漏洞
  文件读取漏洞， 指的是攻击者通过一些手段读取服务器上开发者不允许读到的文件。

  实际上，许多开发人员在进行二次开发时过于信任框架或中间件底层所实现的安全机制，未了解框架对应的安全机制，只是简单的利用 API 文档开发。

  还有一种是服务器本身的问题，或者是服务器不安全的配置导致的。

## 3.1 文件读取漏洞常见触发点
### 3.1.1 Web 语言
1. PHP
  PHP 标准函数，包括但不限于：
  > - file_get_contents()、file()
  > - fopen()、fread()、fgets()
  > - 文件包含相关函数：include()、require()、include_once()、require_once()
  > - 执行系统命令：system()、exec()
  > - 其他 php 扩展函数
   
  **PHP 的 wrapper 和 filter 特色机制**
  - PHP 向用户提供待打开文件的方式不是简简单单的一个路径，而是一个流，它是对数据的抽象，用于统一数据操作。它不管是本地文件还是远程文件，或是压缩文件等等，只要来的是流式数据，操作方法都一样。

  - 每个流都对应一种包装器 wrapper。流是从统一操作这个角度产生的概念，包装器是从理解数据内容出发产生的概念，也就是这个统一的操作方式如何操作或配置不同的内容。比如 http 协议传来的数据是流的方式，但只有 http 包装器才能理解传来数据的意思。

  - 每一种流打开后都可以应用任意数量的过滤器 filter 在上面，它可以对数据进行添加、修改等。
  
  - PHP 的 filter 特性给我们进行任意文件读取提供了很多便利。假如服务端 include 函数路径参数可控，正常情况下它会将目标文件当作 PHP 文件去解析，如果解析文件中存在 `<?PHP`等 PHP 相关标签，那么标签中的内容会被当作代码执行。
  
  - PHP 文件包含的实际问题中，可能遇到三种情况
    1. 文件路径前面可控，后面不可控。PHP 较低版本可以用`\x00`截断，当服务器具有文件上传功能时，可以尝试利用 zip 或 phar 协议直接进行文件包含进而执行 php 代码。
    2. 文件路径后面可控，前面不可控。可以通过`../`进行目录穿越直接读取文件，但这种情况下无法使用 wrapper。如果服务器利用 include 等包含类的函数，无法读取 PHP 文件中的 PHP 代码。
    3. 文件路径中间可控。与第一种类似，无法利用 wrapper 进行文件包含。

2. Python
   python 的 Web 应用倾向于通过自身的模块启动服务，同时搭配中间件、代理服务将整个 Web 应用呈现给用户。用户和 Web 服务的交互过程本身就包含对服务器资源文件的请求，所以容易出现非预期读取文件的情况。Python 框架任意文件读取漏洞也是因为缺乏统一的资源文件交互标准。

   - 漏洞经常出现在框架请求静态资源文件部分，也就是最后读取文件内容的 open 函数，直接漏洞成因往往是开发者忽略了 python 函数的特性。如`os.path.join()`函数
     > >>> os.path.join("/a","/b")
     > '/b'
     > 很多开发者通过判断用户传入路径不好含'.'来保证用户在读取资源时不会发生目录穿越，随后将用户的输入代入`os.path.join()`的第二个参数，但是如果用户传入'/'，依然可以穿越到根目录。
   - 滥用 open 函数、模板的不当渲染导致任意文件读取。
     - 比如将用户输入的某些数据作为文件名的一部分（常见于认证服务或者日志服务）存储在服务器中，在取文件内容的部分也用过将经过处理的用户输入数据作为索引取查找相关文件，这就给攻击者一个进行目录穿越的途径。
   - 攻击者构造软链接放入压缩包，解压后的内容直指服务器相应文件，攻击者访问解压后的链接文件会返回链接指向文件的相应内容。
   - python 的模板注入、反序列化等漏洞都可造成一定程度的任意文件读取，最大危害是导致任意命令执行。

3. java
   - 文件读取函数 FileInputStream、XXE
   - 支持"file://"协议的模块
   - 已知漏洞：Spring Cloud Config Server 路径穿越与任意文件读取漏洞(CVE-2019-3799)、Jenkins 任意文件读取漏洞(CVE-2018-1999002)

4. Ruby
   - CTF 中常与 Rails 框架相关
   - 已知漏洞：Ruby On Rails 远程代码执行漏洞(CVE-2016-0752)、Ruby On Rails 路径穿越与任意文件读取漏洞(CVE-2018-3760)、Ruby On Rails 路径穿越与任意文件读取漏洞(CVE-2019-5418)

5. Node
   - Node.js 的 express 模块曾存在任意文件读取漏洞(CVE-2017-14849)

### 3.1.2 中间件/服务器相关
1. Nginx 错误配置
   Nginx 错误配置导致的文件读取漏洞常与 python-web 应用一起出现，因为 Nginx 一般被视为 python-web 反向代理的最佳实现。
   ```
   location /static{
     alias /home/myapp/static/;
   }
   ```
   如果请求的 Web 路径是 `/static../`，拼接到 alias 上就变成了 `/home/myapp/static/../`，此时产生了目录穿越。

   漏洞的成因是 location 最后没有加“/”限制。

2. 数据库
   通常利用数据库的文件读取操作
   - MySQL 的 `load_file()`函数

3. 软链接
   bash 命令 ln -s 创建一个指向指定文件的软链接文件，将之上传到服务器，再次访问这个文件时实际上请求的是服务器上它指向的文件。

4. FFmpeg 
   FFmpeg 存在任意文件读取漏洞。`https://www.cnblogs.com/iamstudy/articles/2017_quanguo_ctf_web_writeup.html`

5. Docker-API
   Docker-API 可以控制 Docker 的行为，其一般通过 UNIX Socket 通信，也可以通过 HTTP 通信。当存在 SSRF 漏洞时，尤其是可以通过 SSRF 漏洞进行 UNIX Socket 通信的时候，可以操纵 Docker-API 把本地文件载入 Docker 新容器进行读取，从而形成一种另类的任意文件读取。

### 3.1.3 客户端相关
  客户端文件读取漏洞大多是基于 XSS 漏洞读取本地文件。
1. 浏览器 / Flash XSS
2. MarkDown 语法解析器 XSS

## 3.2 文件读取漏洞常见读取路径
### 3.2.1 Linux
1. flag名称（相对路径）
   有时 fuzz flag 名称便可得到答案。
2. 服务器信息（绝对路径）
   1. `/etc目录`：多是应用或系统配置文件，其下文件是进行文件读取的首要目标。
     > 用户名：密码：UID：GID：描述：主目录：默认shell
   2. `/etc/passwd`：linux 系统保存用户信息及其工作目录的文件，一般被用作 linux 系统下文件读取漏洞存在性判断的基准。（所有用户/组可读）
   3. `/etc/shadow`：linux 系统保存用户及密码（hash）的文件。（root用户可读写，shadow 组可读）
   4. `/etc/apache2/*`：Apache 配置文件，可获知 Web 目录、服务器端口等信息。
   5. `/etc/nginx/*`：Nginx 配置文件，可获知 Web 目录，服务器端口等信息。
   6. `/etc/apparmor(.d)`：Apparmor 配置文件，获知各应用系统调用的白名单、黑名单。如查看 MySQL 是否禁用系统调用。
   7. `/etc/(cron.d/*|crontab)`：定时任务文件，可发现隐藏的目录或其他文件。
   8. `/etc/environment`：环境变量配置文件之一，存在大量目录信息泄露，甚至是 secret key 泄露。
   9. `/etc/hostname`：主机名
   10. `/etc/hosts`：主机名查询静态表，包含域名解析 IP 的成对信息，可以探测网卡信息和内网 IP/域名。
   11. `/etc/issue`：系统版本
   12. `/etc/mysql/*`：MySQL 配置文件
   13. `/etc/php/*`：PHP 的配置文件
   14. `/proc目录`：通常存储进程动态运行的信息，本质上是一个虚拟目录。如果要查看非当前进程信息，pid 可以暴力破解，如果要查看当前进程，用`/proc/self`代替`/proc/[pid]`即可。
       1.  `/proc/[pid]/cmdline`：可读取比较敏感的信息，如使用 `mysql-uxxx-pxxxx`登录 MySQL 会在 cmdline 中显示明文密码
       2.  `/proc/[pid]/cwd`：直接跳转到当前目录
       3.  `/proc/[pid]/environ`：环境变量中可能存在 secret_key，通过 environ 进行读取

## 3.3 Windows
  Windows 与 PHP 搭配使用时存在一个问题：可以使用"<"等符号作为通配符，从而在不知道完整文件名的情况下进行文件读取。
  