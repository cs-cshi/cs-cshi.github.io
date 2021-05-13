---
title: 【python】python基础
date: 2021-03-24 22:43:57
tags: 
      - python
categories: 
- [编程语言, python]
---

python 基础语法

<!--more-->

# 1. python 解释器
我们编写的Python代码都要放在Python解释器上运行，解释器是代码与计算机硬件之间的软件逻辑层。根据选用的Python版本的不同，解释器本身可以用C语言实现（Python的官方实现版本）、Java语言实现或者其他的形式。无论使用采用什么版本，编写好的Python程序代码必须放在解释器中运行。

将我们编写好的Python代码放到解释器中运行，此过程主要分为两步，第一步是将源码编译成“字节码”，第二步是将编译好的字节码转发到Python“虚拟机”中运行。当然对于程序员来说，这两步都是隐藏的。

## 1.1 字节码编译
当我们运行Python代码时，Python解释器内部会先通过词法分析器将文件中的每一条代码语句分解为单一步骤，然后编译成字节码的形式。编译只是一个简单的翻译步骤，而且字节码是属于源代码层次范围内的底层代码，是与平台无关的，所以编译好的字节码是可以跨平台运行的。这些字节码可以提高执行速度，比起原始的源代码语句，字节码的运行速度要快很多。

如果Python进程在机器上拥有写入权限，那么它将把编译好的字节码保存为一个以 .pyc 为扩展名的文件。Python这样做是作为一种启动速度的优化，下一次运行程序的时候，如果你在上次保存字节码之后没有修改过源代码，Python将会直接加载 .pyc文件，并跳过编译这个步骤。如果你修改了源代码，下次运行程序时，字节码文件将自动重新创建。

## 1.2 Python虚拟机（PVM）
当源文件编译成字节码，字节码就会发送到Python虚拟机（PVM）上来执行。事实上，PVM就是迭代运行字节码指令的一个大循环，一个接一个的完成操作。从技术上讲，这是Python解释器的最后一步。

和C/C++这类完全编译语言相比，Python的工作中没有 “build” 和 “make” 操作，也就是没有将源码编译成可以直接在机器上运行的二进制代码的过程。而且Python解释器中间形成的**字节码并不是CPU可以直接运行的二进制代码，PVM仍然需要解释字节码成为二进制码，再交由CPU运行**。所以Python代码无法运行的像C/C++一样快。

在程序开始执行之前不需要预编译和连接，只需要简单的输入并运行代码即可，这使得开发周期大大缩短。这同样使得Python具有更多的动态语言特性：在运行时，Python程序可以去构建并执行另一个Python程序，而且往往非常的方便。

## 1.3 python 解释器分类
- CPython：C语言开发，使用最广，默认的解释器
- IPython：基于CPython之上的交互式解释器
- PyPy：采用JIT(Just In Time, 即时编译)技术，对python代码进行动态编译，追求执行速度
- Jython：运行在Java平台上的解释器，可以直接编译成Java字节码执行
- IronPython：同理Jython，运行在 .Net 平台上

# 2. 变量和数据类型
## 2.1 注释
解释器不执行注释内容
1. 单行注释
```python
# 注释内容
```

2. 多行注释
```python
"""
  注释
  注释
  注释
"""
'''
  注释
  注释
  注释
'''
```

## 2.2 变量
> 变量名 = 值
### 2.2.1 标识符
变量名要满足标识符的规则
- 由数字、字⺟、下划线组成
- 不能数字开头
- 不能使⽤内置关键字
- 严格区分⼤⼩写

### 2.2.2 命名习惯
- ⻅名知义。
- ⼤驼峰：即每个单词⾸字⺟都⼤写，例如： MyName 。
- ⼩驼峰：第⼆个（含）以后的单词⾸字⺟⼤写，例如： myName 。
- 下划线：例如： my_name 。
  
## 2.2.3 数据类型
- 数值
- 布尔型
- str（字符串）
- list（列表）
- tuple（元组）
- set（集合）
- dict（字典）
```python
a = 1
print(type(a)) # <class 'int'> -- 整型

b = 1.1
print(type(b)) # <class 'float'> -- 浮点型

c = True
print(type(c)) # <class 'bool'> -- 布尔型

d = '12345'
print(type(d)) # <class 'str'> -- 字符串

e = [10, 20, 30]
print(type(e)) # <class 'list'> -- 列表

f = (10, 20, 30)
print(type(f)) # <class 'tuple'> -- 元组

h = {10, 20, 30}
print(type(h)) # <class 'set'> -- 集合

a = 1
print(type(a)) # <class 'int'> -- 整型

b = 1.1
print(type(b)) # <class 'float'> -- 浮点型

c = True
print(type(c)) # <class 'bool'> -- 布尔型

d = '12345'
print(type(d)) # <class 'str'> -- 字符串

e = [10, 20, 30]
print(type(e)) # <class 'list'> -- 列表

f = (10, 20, 30)
print(type(f)) # <class 'tuple'> -- 元组

h = {10, 20, 30}
print(type(h)) # <class 'set'> -- 集合
```

# 3. 输入输出
## 3.1 输出
```python
print('hello Python')
age = 18
print(age)
```
### 3.1.1 格式化输出
| 格式符号 | 转换 |
| --- | --- |
| %s | 字符串 |
| %d | 有符号的⼗进制整数 |
| %f | 浮点数 |
| %c | 字符 |
| %u | ⽆符号⼗进制整数 |
| %o | ⼋进制整数 |
| %x | ⼗六进制整数（⼩写ox） |
| %X | ⼗六进制整数（⼤写OX） |
| %e | 科学计数法（⼩写'e'） |
| %E | 科学计数法（⼤写'E'） |
| %g | %f和%e的简写 |
| %G | %f和%E的简写 |

- %06d，表示输出的整数显示位数，不⾜以0补全，超出当前位数则原样输出
- %.2f，表示⼩数点后显示的⼩数位数。

```python
age = 18
name = 'TOM'
weight = 75.5
student_id = 1

# 我的名字是TOM
print('我的名字是%s' % name)

# 我的学号是0001
print('我的学号是%4d' % student_id)

# 我的体重是75.50公⽄
print('我的体重是%.2f公⽄' % weight)

# 我的名字是TOM，今年18岁了
print('我的名字是%s，今年%d岁了' % (name, age))

# 我的名字是TOM，明年19岁了
print('我的名字是%s，明年%d岁了' % (name, age + 1))

# 我的名字是TOM，明年19岁了
print(f'我的名字是{name}, 明年{age + 1}岁了')
# f-格式化字符串是Python3.6中新增的格式化⽅法，该⽅法更简单易读。
```

### 3.1.2 转义字符
转义字符在书写形式上由多个字符组成，但 Python 将它们看作是一个整体，表示一个字符。
| 转义字符 | 说明 |
| --- | --- |
| \n | 换行符，将光标位置移到下一行开头。 |
| \r | 回车符，将光标位置移到本行开头。 |
| \t | 水平制表符，也即 Tab 键，一般相当于四个空格。 |
| \a | 蜂鸣器响铃。注意不是喇叭发声，现在的计算机很多都不带蜂鸣器了，所以响铃不一定有效。 |
| \b | 退格（Backspace），将光标位置移到前一列。 |
| \\ | 反斜线 |
| \' | 单引号 |
| \" | 双引号 |
| \	| 在字符串行尾的续行符，即一行未完，转到下一行继续写。 |

### 3.1.3 结束符
在Python中，print()， 默认⾃带 end="\n" 这个换⾏结束符，可以按需求更改结束符。
```python
    print('内容', end="\n")
```

## 3.2 输入
```python
input_message = input("提示信息:")
```
### 3.2.1 特点
- 当程序执⾏到 input ，等待⽤户输⼊，输⼊完成之后才继续向下执⾏。
- ⼀般将input接收的数据存储到变量，⽅便使⽤。
- input接收的任何数据默认都是**字符串**类型
```python
password = input('请输⼊您的密码：')
print(f'您输⼊的密码是{password}')

print(type(password)) # <class 'str'>
```

# 4. 转换数据类型和运算符
## 4.1 转换数据类型的函数
| 函数 | 说明 |
| --- | --- |
| int(x [,base ]) | 将x转换为⼀个整数 |
| float(x ) | 将x转换为⼀个浮点数 |
| complex(real [,imag ]) | 创建⼀个复数，real为实部，imag为虚部 |
| str(x ) | 将对象 x 转换为字符串 |
| repr(x ) | 将对象 x 转换为表达式字符串 |
| eval(str ) | ⽤来计算在字符串中的有效Python表达式,并返回⼀个对象 |
| tuple(s ) | 将序列 s 转换为⼀个元组 |
| list(s ) | 将序列 s 转换为⼀个列表 |
| chr(x ) | 将⼀个整数转换为⼀个Unicode字符 |
| ord(x ) | 将⼀个字符转换为它的ASCII整数值 |
| hex(x ) | 将⼀个整数转换为⼀个⼗六进制字符串 |
| oct(x ) | 将⼀个整数转换为⼀个⼋进制字符串 |
| bin(x ) | 将⼀个整数转换为⼀个⼆进制字符串 |
```python
# 1. float() -- 转换成浮点型
num1 = 1
print(float(num1))
print(type(float(num1)))

# 2. str() -- 转换成字符串类型
num2 = 10
print(type(str(num2)))

# 3. tuple() -- 将⼀个序列转换成元组
list1 = [10, 20, 30]
print(tuple(list1))
print(type(tuple(list1)))

# 4. list() -- 将⼀个序列转换成列表
t1 = (100, 200, 300)
print(list(t1))
print(type(list(t1)))

# 5. eval() -- 将字符串中的数据转换成Python表达式原本类型
str1 = '10'
str2 = '[1, 2, 3]'
str3 = '(1000, 2000, 3000)'
print(type(eval(str1)))
print(type(eval(str2)))
print(type(eval(str3)))
```

## 4.2 运算符
### 4.2.1 算数运算符
| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| + | 加 | 1 + 1 输出结果为 2 |
| - | 减 | 1-1 输出结果为 0 |
| * | 乘 | 2 * 2 输出结果为 4 |
| / | 除 | 10 / 2 输出结果为 5 |
| // | 整除 | 9 // 4 输出结果为2 |
| % | 取余 | 9 % 4 输出结果为 1 |
| ** | 指数 | 2 ** 4 输出结果为 16，即 2 * 2 * 2 * 2 |
| () | ⼩括号 | ⼩括号⽤来提⾼运算优先级，即 (1 + 2) * 3 输出结果为 9 |
优先级顺序： () ⾼于 ** ⾼于 * / // % ⾼于 + -

### 4.2.2 赋值运算符
- 单个变量赋值
```python
num = 1
```
- 多个变量赋值
```python
num1, float1, str1 = 10, 0.5, 'hello world'
# 等价于
num = 10 
float1 = 0.5
str1 = 'hello world'

a = b = 10 # 多变量赋相同值
```

### 4.2.3  复合赋值运算符
| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| += | 加法赋值运算符 | c += a 等价于 c = c + a |
| -= | 减法赋值运算符 | c -= a 等价于 c = c- a |
| *= | 乘法赋值运算符 | c *= a 等价于 c = c * a |
| /= | 除法赋值运算符 | c /= a 等价于 c = c / a |
| //= | 整除赋值运算符 | c //= a 等价于 c = c // a |
| %= | 取余赋值运算符 | c %= a 等价于 c = c % a |
| **= | 幂赋值运算符 | c ** = a 等价于 c = c ** a |

### 4.2.4 比较运算符
```python
a = 7
b = 5
print(a == b) # False
print(a != b) # True
print(a < b) # False
print(a > b) # True
print(a <= b) # False
print(a >= b) # True
```

### 4.2.5 逻辑运算符
```python
a = 1
b = 2
c = 3

# 与
print((a < b) and (b < c)) # True
print((a > b) and (b < c)) # False

# 或
print((a > b) or (b < c)) # True

# 非
print(not (a > b)) # True
```

# 5. 条件语句
## 5.1 语法
- if 语句语法
```python
if 条件:
    条件成⽴执⾏的代码
```
- if...else...
```python
if 条件:
    条件成⽴执⾏的代码
else:
    条件不成⽴执⾏的代码
```

- 多重判断
```python
if 条件1:
    条件1成⽴执⾏的代码
elif 条件2:
    条件2成⽴执⾏的代码
else:
    以上条件都不成⽴执⾏的代码
```
- if嵌套
```python
if 条件1:
    条件1成⽴执⾏的代码
    if 条件2:
        条件2成⽴执⾏的代码
 
```

## 5.2 三元运算符
语法
> 条件成⽴执⾏的表达式 if 条件 else 条件不成⽴执⾏的表达式

```python
a = 1
b = 2
c = a if a > b else b
print(c)
```

# 6. 循环
## 6.1 while 循环
```python
i = 1
result = 0
while i <= 100:
    result += i
    i += 1
# 输出5050
print(result)
```

## 6.2 for 循环
```python
str1 = '12345678'
for i in str1:
    print(i)
```
## 6.3 break, continue, else
- break：直接退出循环
- continue：跳过当前轮循环continue后代码，直接进行下一轮（计数器+1）
- else
  - while和for都可以配合else使⽤
  - else下⽅缩进的代码含义：当循环正常结束后执⾏的代码
  - **break终⽌循环不会执⾏else下⽅缩进的代码**
  - **continue退出循环的⽅式执⾏else下⽅缩进的代码**

# 7. 字符串
## 7.1 认识字符串
### 7.1.1 语法
⼀般使⽤引号来创建字符串。
- 一对引号字符串
```python
a = 'hello world'
b = "abcdefg"
```
- 三引号字符串
```python
name3 = ''' Tom '''
name4 = """ Rose """
a = ''' i am Tom,
    nice to meet you! '''
b = """ i am Rose,
    nice to meet you! """
```
- 特殊字符的使用
```python
c = "I'm Tom"
d = 'I\'m Tom'
```
### 7.1.2 字符串输出
```python
print('hello world')
name = 'Tom'
print('我的名字是%s' % name)
print(f'我的名字是{name}')
```
### 7.1.3 字符串输入
在Python中，使⽤ input() 接收⽤户输⼊。默认接收为字符串类型。
```python
name = input('请输⼊您的名字：')
print(f'您输⼊的名字是{name}')
print(type(name))
password = input('请输⼊您的密码：')
print(f'您输⼊的密码是{password}')
print(type(password))
```

## 7.2 下标
```python
name = "abcdef"
print(name[1]) # b
print(name[0]) # a
print(name[2]) # c
```
> 下标从 0 开始

## 7.3 切片
切⽚是指对操作的对象截取其中⼀部分的操作。**字符串、列表、元组都⽀持切⽚操作**。
### 7.3.1 语法
> 序列[开始位置下标:结束位置下标:步⻓]
> - 不包含结束位置下标对应的数据， 正负整数均可；（左闭右开[)）
> - 步⻓是选取间隔，正负整数均可，默认步⻓为1。

### 7.3.2 演示
```python
name = "abcdefg"
print(name[2:5:1]) # cde
print(name[2:5]) # cde
print(name[:5]) # abcde
print(name[1:]) # bcdefg
print(name[:]) # abcdefg
print(name[::2]) # aceg
print(name[:-1]) # abcdef, 负1表示倒数第⼀个数据
print(name[-4:-1]) # def
print(name[::-1]) # gfedcba
```

## 7.4 常用⽅法
### 7.4.1 查找
| 函数 | 语法 | 说明 |
| --- | --- |--- |
| find() | 字符串序列.find(⼦串, 开始位置下标, 结束位置下标) |检测某个⼦串是否包含在这个字符串中，如果在返回这个⼦串开始的位置下标，否则则返回-1。 |
| index() | 字符串序列.index(⼦串, 开始位置下标, 结束位置下标) | 检测某个⼦串是否包含在这个字符串中，如果在返回这个⼦串开始的位置下标，否则则报异常。 |
| rfind() |  | 和find()功能相同，但查找⽅向为右侧开始。 |
| rindex() | | 和index()功能相同，但查找⽅向为右侧开始。 |
| count() | 字符串序列.count(⼦串, 开始位置下标, 结束位置下标) | 返回某个⼦串在字符串中出现的次数 |

### 7.4.2 修改
| 函数 | 语法 | 说明 |
| --- | --- |--- |
| replace() |  字符串序列.replace(旧⼦串, 新⼦串, 替换次数) | 替换 |
| split() | 字符串序列.split(分割字符, num) | 按照指定字符分割字符串。num，替换次数 |
| join() | 字符或⼦串.join(多字符串组成的序列) | ⽤⼀个字符或⼦串合并字符串，即是将多个字符串合并为⼀个新的字符串。|
| capitalize() | mystr.capitalize() | 将字符串第⼀个字符转换成⼤写。|
| title() | mystr.title() | 将字符串每个单词⾸字⺟转换成⼤写。 |
| lower() | mystr.lower() | 将字符串中⼤写转⼩写。|
| upper() | mystr.upper() | 将字符串中⼩写转⼤写。|
| lstrip() | mystr.lstrip() | 删除字符串左侧空⽩字符。|
| rstrip() | mystr.rstrip() | 删除字符串右侧空⽩字符。|
| strip() | mystr.strip() | 删除字符串两侧空⽩字符。|
| ljust() | mystr.ljust() | 返回⼀个原字符串左对⻬,并使⽤指定字符(默认空格)填充⾄对应⻓度的新字符串。|
> 注意：
> - 字符串属于不可变类型，修改的时候其他不是修改原本的字符串，而是返回一个新的字符串。
```python
list1 = ('aa', 'b', 'cc', 'ddd')
print('_'.join(list1)) # 结果：aa_b_cc_ddd
print('...'.join(t1))  # 结果：aa...b...cc...ddd
```
### 7.4.3 判断
| 函数 | 语法 | 说明 |
| --- | --- |--- |
| startswith() | 字符串序列.startswith(⼦串, 开始位置下标, 结束位置下标) | 检查字符串是否是以指定⼦串开头，是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查。|
| endswith() | 字符串序列.endswith(⼦串, 开始位置下标, 结束位置下标) | 检查字符串是否是以指定⼦串结尾，是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查。| 
| isalpha() |  | 如果字符串不为空并且所有字符都是字⺟则返回 True, 否则返回 False。|
| isdigit() |  | 如果字符串只包含数字则返回 True 否则返回 False。|
| isalnum() |  | 如果字符串不为空并且所有字符都是字⺟或数字则返回 True,否则返回 False。|
| isspace() |  | 如果字符串中只包含空⽩，则返回 True，否则返回 False。|

# 8. 列表和元组
## 8.1 列表
### 8.1.1 语法
列表可以⼀次性存储多个数据，且可以为不同数据类型。
```python
[数据1, 数据2, 数据3, 数据4......]
```
### 8.1.2 常用方法
#### 1. 增加
| 函数 | 语法 | 说明 |
| --- | --- |--- |
| append() | 列表序列.append(数据) | 列表结尾追加数据。|
| extend() | 列表序列.extend(数据) | 列表结尾追加数据，如果数据是⼀个序列，则将这个序列的数据逐⼀添加到列表。|
| insert() | 列表序列.insert(位置下标, 数据) | 指定位置新增数据。|
#### 2. 删除
| 函数 | 语法 | 说明 |
| --- | --- |--- |
| del |  del ⽬标 | 删除数据，可以是整个列表，也可以是列表中的某个元素 |
| pop() | 列表序列.pop(下标) | 删除指定下标的数据(默认为最后⼀个)，并返回该数据。|
| remove() | 列表序列.remove(数据) |移除列表中某个数据的第⼀个匹配项。|
| clear() |  | 清空列表 |
```python
name_list = ['Tom', 'Lily', 'Rose']
del name_list[0]
print(name_list)  # 结果：['Lily', 'Rose']
del name_list
```
#### 3. 修改
> 可以通过坐标直接修改

| 函数 | 语法 | 说明 |
| --- | --- |--- |
| reverse() |  | 逆置 |
| sort() |  | 排序 |

#### 4. 查找
  既可以通过下标查找，也可以通过函数查找。

| 函数 | 语法 | 说明 |
| --- | --- |--- |
| index() | 列表序列.index(数据, 开始位置下标, 结束位置下标) | 返回指定数据所在位置的下标。|
| count() | list1.count(data) | 统计指定数据在当前列表中出现的次数。|
| len() |  | 访问列表⻓度，即列表中数据的个数。|
| in |  | 判断指定数据在某个列表序列，如果在返回True，否则返回False。|
| not in |  | 判断指定数据不在某个列表序列，如果不在返回True，否则返回False。|
```python
name_list = ['Tom', 'Lily', 'Rose']
name = input('请输⼊您要搜索的名字：')

if name in name_list:
    print(f'您输⼊的名字是{name}, 名字已经存在')
else:
    print(f'您输⼊的名字是{name}, 名字不存在')
```
- 复制 copy()

## 8.2 元组
元组与列表十分相似，可以一次存储多个不同类型的数据，但**元组中的数据不可修改.**。

**如果元组里面有列表，列表里的数据是可以修改的。**

### 8.2.1 语法
使⽤⼩括号，且逗号隔开各个数据，数据可以是不同的数据类型。
```python
# 多个数据元组
t1 = (10, 20, 30)

# 单个数据元组
t2 = (10,)
```

注意：如果定义的元组只有⼀个数据，那么这个数据后⾯也好添加逗号
```python
t2 = (10,)
print(type(t2)) # tuple

t3 = (20)
print(type(t3)) # int

t4 = ('hello')
print(type(t4)) # str
```

### 8.2.2 常见方法
> 元组可以用下标查找数据

| 函数 | 语法 | 说明 |
| --- | --- |--- |
| index() |  | 查找某个数据，如果数据存在返回对应的下标，否则报错，语法和列表、字符串的 index ⽅法相同。|
| count() |  | 统计某个数据在当前元组出现的次数。|
| len() |  | 统计元组中数据的个数。|
```python
tuple1 = ('aa', 'bb', 'cc', 'bb')
tuple1[0] = 'aaa'  # 报错，不能直接修改元组内的数据

tuple2 = (10, 20, ['aa', 'bb', 'cc'], 50, 30)
print(tuple2[2])  # 访问到列表，结果：['aa', 'bb', 'cc']
tuple2[2][0] = 'aaaaa'  # 可以修改元组里的列表里的数据
print(tuple2)  # 结果：(10, 20, ['aaaaa', 'bb', 'cc'], 50, 30)
```

# 9. 字典
字典的数据以键值对形式存储，数据无序，所以字典不⽀持下标。
## 9.2 语法
```python
# 数据初始化字典
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

# 空字典
dict2 = {}
dict3 = dict()
```

## 9.3 常见操作
1. 增
字典序列[key] = 值
> 如果key存在则修改这个key对应的值；如果key不存在则新增此键值对。
```python
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

dict1['name'] = 'Rose'
# 结果：{'name': 'Rose', 'age': 20, 'gender': '男'}
print(dict1)

dict1['id'] = 110
# {'name': 'Rose', 'age': 20, 'gender': '男', 'id': 110}
print(dict1)
```
2. 删
  - del() / del：删除字典或删除字典中指定键值对。
  - clear()：清空字典
```python
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
del dict1['gender']
# 结果：{'name': 'Tom', 'age': 20}

dict1.clear()  # {}
```
3. 查
- key值查找
```python
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
print(dict1['name']) # Tom
# 存在时返回，不存在时报错
```
- get(key, 默认值)，不存在时返回默认值
- keys()
- values()
- items()
```python
dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

print(dict1.items())  
# dict_items([('name', 'Tom'), ('age', 20), ('gender', '男')])
```

## 9.4 循环遍历
- 遍历 key
  ```python
  dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
  for key in dict1.keys():
      print(key)
  ```
- 遍历 value
  ```python
  dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
  for value in dict1.values():
      print(value)
  ```
- 遍历元素
  ```python
  dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
  # item
  for item in dict1.items():
      print(item)
  
  # k,v
  for key, value in dict1.items():
      print(f'{key} = {value}')
  ```

# 10. 集合
## 10.1 语法
创建集合使⽤ {} 或 set() ， 但是如果要**创建空集合只能使⽤ set()** ，因为 {} ⽤来创建空字典。
**set 去重原理： 先__hash__，再__eq__**
- 特点
  - 数据不重复
  - 数据无序，不支持下标
## 10.2 常见方法
| 函数 | 语法 | 说明 |
| --- | --- |--- |
| add() |  | 增加元素，如果已存在于集合中，不进行任何操作 |
| update |  | 追加数据，必须是序列 |
| remove() |  | 删除集合中的指定数据，如果**数据不存在则报错**。|
| discard() |  | 删除集合中的指定数据，如果**数据不存在也不会报错**。|
| pop() |  | 随机删除集合中的某个数据，并返回这个数据。|
| in |  | 判断数据在集合序列 |
| not in |  | 判断数据不在集合序列 |

```python
s1 = {10, 20}
s1.add(100)
s1.add(10)
print(s1)  # {100, 10, 20}

s1 = {10, 20}
# s1.update(100) # 报错
s1.update([100, 200])
s1.update('abc')
print(s1)  # { 'b', 'a', 'c', 200, 100 , 10, 20 }
```

# 11. 容器类型公有方法
## 11.1 运算符
| 运算符 | 描述 | ⽀持的容器类型 |
| --- | --- | --- |
| + | 合并 | 字符串、列表、元组 |
| * | 复制 | 字符串、列表、元组 |
| in | 元素是否存在 | 字符串、列表、元组、字典 |
| not in | 元素是否不存在 | 字符串、列表、元组、字典 |
```python
# 1. 字符串
print('-' * 10)  # ----------

# 2. 列表
list1 = ['hello']
print(list1 * 4)  # ['hello', 'hello', 'hello', 'hello']

# 3. 元组
t1 = ('world',)
print(t1 * 4)  # ('world', 'world', 'world', 'world')
```

## 11.2 方法
| 函数 | 描述 |
| --- | --- |
| len() | 计算容器中元素个数 |
| del 或 del() | 删除 |
| max() | 返回容器中元素最⼤值 |
| min() | 返回容器中元素最⼩值 |
| range(start,end, step) | ⽣成从start到end的数字，步⻓为 step，供for循环使⽤ |
| enumerate() | ⽤于将⼀个可遍历的数据对象(如列表、元组或字符串)组合为⼀个索引序列，同时列出数据和数据下标，⼀般⽤在 for 循环当中。|

enumerate(可遍历对象, start=0)
> start参数⽤来设置遍历数据的下标的起始值，默认为0。
```python
list1 = ['a', 'b', 'c', 'd', 'e']

for i in enumerate(list1):
    print(i)
# (0, 'a')
# (1, 'b')
#   ... 
# (4m 'e')

for index, char in enumerate(list1, start=1):
    print(f'下标是{index}, 对应的字符是{char}')
    # start = 1，下标从1开始，而不是0
```

## 11.3 容器类型转换
python 常见的几种容器：str, list, tuple, dict, set
- tuple()
```python
list1 = [10, 20, 30, 40, 50, 20]
s1 = {100, 200, 300, 400, 500}  # set
print(tuple(list1))
print(tuple(s1))
```
- list()
```python
t1 = ('a', 'b', 'c', 'd', 'e')
s1 = {100, 200, 300, 400, 500}
print(list(t1))
print(list(s1))
```
- set()
```python
list1 = [10, 20, 30, 40, 50, 20]
t1 = ('a', 'b', 'c', 'd', 'e')
print(set(list1))
print(set(t1))
```

# 12. 推导式
## 12.1 列表推导式
⽤⼀个表达式创建⼀个有规律的列表或控制⼀个有规律列表。
- 简单的列表推导式
```python
list1 = [i for i in range(10)] 
print(list1)  # 创建⼀个0-10的列表。

list2 = [i for i in range(0, 10, 2)]  # 创建一个 0-10 的偶数列表
```
- 带if的列表推导式
```python
list1 = [i for i in range(10) if i % 2 == 0]  # 创建一个 0-10 的偶数列表
```
-  多个for循环实现列表推导式
```python
list1 = [(i, j) for i in range(1, 3) for j in range(3)]
# [(1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
```
## 12.2 字典推导式
字典推导式作⽤：快速合并列表为字典或提取字典中⽬标数据。
```python
# 1. 创建⼀个字典：字典key是1-5数字，value是这个数字的2次⽅。
dict1 = {i: i**2 for i in range(1, 5)}
print(dict1) # {1: 1, 2: 4, 3: 9, 4: 16}

# 2. 将两个列表合并为⼀个字典
list1 = ['name', 'age', 'gender']
list2 = ['Tom', 20, 'man']
dict1 = {list1[i]: list2[i] for i in range(len(list1))}

# 3. 提取字典中⽬标数据
counts = {'MBP': 268, 'HP': 125, 'DELL': 201, 'Lenovo': 199, 'acer': 99}
count1 = {key: value for key, value in counts.items() if value >= 200}
print(count1) # {'MBP': 268, 'DELL': 201}
```

## 12.3 集合推导式
```python
list1 = [1, 1, 2]
set1 = {i ** 2 for i in list1}
print(set1) # {1, 4}
```

# 13. 函数
## 13.1 语法
```python
def 函数名(参数):
    """ 说明⽂档的位置 """
    code
    ...
```
- 查看函数说明文档
  - help(func)

## 13.2 变量作用域
- 局部变量
  - 定义在函数体内部的变量，只在函数体内部⽣效。
- 全局变量
  - 定义在函数体内、外都能⽣效的变量。

## 13.3 函数的返回值
- 返回多个返回值
  ```python
  def return_num():
      return 1, 2
  
  result = return_num()
  print(result)  # (1, 2)
  ```

## 13.4 函数参数
- 位置参数
  - 根据函数定义的位置来传递参数
    ```python
    def user_info(name, age, gender):
        print(f'您的名字是{name}, 年龄是{age}, 性别是{gender}')

    user_info('TOM', 20, '男')
    ```
- 关键字参数
  - 通过“键=值”形式加以指定，可以让函数更加清晰、容易使⽤，同时也清除了参数的顺序需
求。
  - 函数调⽤时，如果有位置参数时，**位置参数必须在关键字参数的前⾯**，但**关键字参数之间不存在
先后顺序**。
    ```python
    def user_info(name, age, gender):
        print(f'您的名字是{name}, 年龄是{age}, 性别是{gender}')

    user_info('Rose', age=20, gender='⼥')
    user_info('⼩明', gender='男', age=16)
    ```
- 缺省参数
  - 缺省参数也叫默认参数，⽤于定义函数，为参数提供默认值，调⽤函数时可不传该默认参数的值
  - 所有位置参数必须出现在默认参数前，包括函数定义和调⽤
    ```python
    def user_info(name, age, gender='男'):
        print(f'您的名字是{name}, 年龄是{age}, 性别是{gender}')

    user_info('TOM', 20)
    user_info('Rose', 18, '⼥')
    ```
- 不定⻓参数
  - 也叫可变参数。分为包裹(packing)位置参数，和包裹关键字参数。
    ```python
    # 1. 包裹位置传参
    def user_info(*args):
        print(args)
    
    user_info('TOM')  # ('TOM',)
    user_info('TOM', 18)  # ('TOM', 18)

    # 2. 包裹关键字传参
    def user_info(**kwargs):
        print(kwargs)
   
    user_info(name='TOM', age=18, id=110)  # {'name': 'TOM', 'age': 18, 'id': 110}
    ```

## 13.5 拆包
- 元组(tuple)拆包
  ```python
  def return_num():
      return 100, 200

  num1, num2 = return_num()
  print(num1)  # 100
  print(num2)  # 200
  ```
- 字典(dict)拆包
  ```python
  dict1 = {'name': 'TOM', 'age': 18}
  a, b = dict1

  # 对字典进⾏拆包，取出来的是字典的key
  print(a)  # name
  print(b)  # age
  print(dict1[a])  # TOM
  print(dict1[b])  # 18
  ```

## 13.6 引用
在python中，值是靠引⽤来传递来的。可以⽤ id() 来判断两个变量是否为同⼀个值的引⽤。
- 引⽤当做实参
  ```python
  def test1(a):
      print(a)
      print(id(a))

  a += a
  
  print(a)
  print(id(a))

  # int：计算前后id值不同
  b = 100
  test1(b)

  # 列表：计算前后id值相同
  c = [11, 22]
  test1(c)
  ```
## 13.7 可变和不可变类型
所谓可变类型与不可变类型是指：数据能够直接进⾏修改，如果能直接修改那么就是可变，否则是不可变.
- 可变类型
  - 列表
  - 字典
  - 集合
- 不可变类型
  - 整型
  - 浮点型
  - 字符串
  - 元组
  

## 13.8 lambda 表达式
如果⼀个函数有⼀个返回值，并且只有⼀句代码，可以使⽤ lambda简化。
### 13.8.1 语法
lambda 参数列表 ： 表达式
- lambda表达式的参数可有可⽆，函数的参数在lambda表达式中完全适⽤。
- lambda表达式能接收任何数量的参数但只能返回⼀个表达式的值。
```python

# 函数
def fn1():
    return 200
print(fn1)  # 内存地址
print(fn1())  # 200

# lambda表达式
fn2 = lambda: 100
print(fn2)  # 内存地址
print(fn2())  # 100
```

### 13.8.2 参数形式
- 无参数
  ```python
  fn1 = lambda: 100
  print(fn1())  # 100
  ```
- 一个参数
  ```python
  fn1 = lambda a: a
  print(fn1('hello world'))  # hello world
  ```
- 默认参数
  ```python
  fn1 = lambda a, b, c=100: a + b + c
  print(fn1(10, 20))  # 130 : 10+20+100
  ```
- 可变参数：*args
  ```python
  fn1 = lambda *args: args
  print(fn1(10, 20, 30))  # (10,20,30) 元组
  ```
- 可变参数：**kwargs
  ```python
  fn1 = lambda **kwargs: kwargs
  print(fn1(name='python', age=20))  # {'name': 'python', 'age': 20}
  ```  
### 13.8.3 应用
- 带判断的lambda
  ```python
  fn1 = lambda a, b: a if a > b else b
  print(fn1(1000, 500))
  ```
- 列表数据按字典key的值排序
  ```python
  students = [
    {'name': 'TOM', 'age': 20},
    {'name': 'ROSE', 'age': 19},
    {'name': 'Jack', 'age': 22}
  ]
  # 按name值升序排列
  students.sort(key=lambda x: x['name'])
  print(students)  # [{'name': 'Jack', 'age': 22}, {'name': 'ROSE', 'age': 19}, {'name': 'TOM', 'age': 20}]

  # 按name值降序排列
  students.sort(key=lambda x: x['name'], reverse=True)
  print(students)  # [{'name': 'TOM', 'age': 20}, {'name': 'ROSE', 'age': 19}, {'name': 'Jack', 'age': 22}]

  # 按age值升序排列
  students.sort(key=lambda x: x['age'])
  print(students)  # [{'name': 'ROSE', 'age': 19}, {'name': 'TOM', 'age': 20}, {'name': 'Jack', 'age': 22}]
  ```

## 13.9 内置高阶函数
- map()
  - map(func, lst)，将传⼊的函数变量func作⽤到lst变量的每个元素中，并将结果组成新的列表(Python2)/迭代器(Python3)返回。
    ```python
    list1 = [1, 2, 3, 4, 5]
    def func(x):
        return x ** 2
    
    result = map(func, list1)
    print(result) # <map object at 0x0000013769653198>
    print(list(result)) # [1, 4, 9, 16, 25]
    # 注意 result 经过 list 类型转换后，result 变为空
    ```
- reduce()
  - reduce(func，lst)，其中func必须有两个参数。每次func计算的结果继续和序列的下⼀个元素做累积计算。
    ```python
    import functools
    
    list1 = [1, 2, 3, 4, 5]
    
    def func(a, b):  # 上一次函数执行的结果会当作参数传递到下一次的函数（参数 a）
        return a + b
    
    result = functools.reduce(func, list1)
    print(result)  # 15
    ```
- filter()
  - filter(func, lst)函数⽤于过滤序列, 过滤掉不符合条件的元素, 返回⼀个 filter 对象。如果要转换为列表,可以使⽤ list() 来转换。
    ```python
    list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

    def func(x):
        return x % 2 == 0
    
    result = filter(func, list1)
    print(result)  # 内存地址
    print(list(result))  # [2, 4, 6, 8, 10]
    ```

# 14. 文件操作
## 14.1 打开模式
在python，使⽤open函数，可以打开⼀个已经存在的⽂件，或者创建⼀个新⽂件，语法如下：
> open(name, mode)
>  - name：是要打开的⽬标⽂件名的字符串(可以包含⽂件所在的具体路径)。
>  - mode：设置打开⽂件的模式(访问模式)：只读、写⼊、追加等。


| 模式 | 描述 |
| --- | --- |
| r | 以只读⽅式打开⽂件。⽂件的指针将会放在⽂件的开头。这是默认模式。|
| rb | 以⼆进制格式打开⼀个⽂件⽤于只读。⽂件指针将会放在⽂件的开头。这是默认模式。|
| r+ | 打开⼀个⽂件⽤于读写。⽂件指针将会放在⽂件的开头。|
| rb+ | 以⼆进制格式打开⼀个⽂件⽤于读写。⽂件指针将会放在⽂件的开头。|
| w | 打开⼀个⽂件只⽤于写⼊。如果该⽂件已存在则打开⽂件，并从开头开始编辑，即原有内容会被删除。如果该⽂件不存在，创建新⽂件。|
| wb | 以⼆进制格式打开⼀个⽂件只⽤于写⼊。如果该⽂件已存在则打开⽂件，并从开头开始编辑，即原有内容会被删除。如果该⽂件不存在，创建新⽂件。|
| w+ | 打开⼀个⽂件⽤于读写。如果该⽂件已存在则打开⽂件，并从开头开始编辑，即原有内容会被删除。如果该⽂件不存在，创建新⽂件。|
| wb+ | 以⼆进制格式打开⼀个⽂件⽤于读写。如果该⽂件已存在则打开⽂件，并从开头开始编辑，即原有内容会被删除。如果该⽂件不存在，创建新⽂件。|
| a | 打开⼀个⽂件⽤于追加。如果该⽂件已存在，⽂件指针将会放在⽂件的结尾。也就是说，新的内容将会被写⼊到已有内容之后。如果该⽂件不存在，创建新⽂件进⾏写⼊。|
| ab | 以⼆进制格式打开⼀个⽂件⽤于追加。如果该⽂件已存在，⽂件指针将会放在⽂件的结尾。也就是说，新的内容将会被写⼊到已有内容之后。如果该⽂件不存在，创建新⽂件进⾏写⼊。|
| a+ | 打开⼀个⽂件⽤于读写。如果该⽂件已存在，⽂件指针将会放在⽂件的结尾。⽂件打开时会是追加模式。如果该⽂件不存在，创建新⽂件⽤于读写。ab+ 以⼆进制格式打开⼀个⽂件⽤于追加。如果该⽂件已存在，⽂件指针将会放在⽂件的结尾。如果该⽂件不存在，创建新⽂件⽤于读写。|

## 14.2 ⽂件对象⽅法
### 14.2.1 写
> 对象.write('内容')

```python
# 1. 打开⽂件
f = open('test.txt', 'w')

# 2.⽂件写⼊
f.write('hello world')

# 3. 关闭⽂件
f.close()
```
> 注意：
>  1. w 和 a 模式：如果⽂件不存在则创建该⽂件；如果⽂件存在， w 模式先清空再写⼊， a 模式直接末尾追加。
>  2. r 模式：如果⽂件不存在则报错。

### 14.2.2 读
- read()
  - ⽂件对象.read(num), num表示要从⽂件中读取的数据的⻓度（单位是字节），如果没有传⼊num，那么就表示读取⽂件中所有的数据。
- readlines()
  - readlines可以按照⾏的⽅式把整个⽂件中的内容进⾏⼀次性读取，并且返回的是⼀个列表，其中每⼀⾏的数据为⼀个元素。  
- readline()
  - ⼀次读取⼀⾏内容。
- seek()
  - ⽂件对象.seek(偏移量, 起始位置)。⽤来移动⽂件指针。
    - 0：⽂件开头
    - 1：当前位置
    - 2：⽂件结尾

## 14.3 文件备份
```python
old_name = input('请输⼊您要备份的⽂件名：')
index = old_name.rfind('.')

if index > 0:
    postfix = old_name[index:]

new_name = old_name[:index] + '[备份]' + postfix

old_f = open(old_name, 'rb')
new_f = open(new_name, 'wb')

while True:
    con = old_f.read(1024)
    if len(con) == 0:
        break
    new_f.write(con)

old_f.close()
new_f.close()
```

## 14.4 ⽂件和⽂件夹的操作
在Python中⽂件和⽂件夹的操作要借助os模块⾥⾯的相关功能
```python
import os
```
| 函数 | 说明 |
| --- | --- |
| os.rename(⽬标⽂件名, 新⽂件名) | ⽂件重命名 |
| os.rename(⽬标⽂件名, 新⽂件名) | 删除⽂件 |
| os.mkdir(⽂件夹名字) | 创建⽂件夹 |
| os.rmdir(⽂件夹名字) | 删除⽂件夹 |
| os.getcwd() | 获取当前⽬录 |
| os.chdir(⽬录) | 改变默认⽬录 |
| os.listdir(⽬录) | 获取⽬录列表 |
```python
import os
# 设置重命名标识：如果为1则添加指定字符，flag取值为2则删除指定字符
flag = 1

# 获取指定⽬录
dir_name = './'

# 获取指定⽬录的⽂件列表
file_list = os.listdir(dir_name)
# print(file_list)

# 遍历⽂件列表内的⽂件
for name in file_list:
    # 添加指定字符
    if flag == 1:
        new_name = 'Python-' + name
    # 删除指定字符
    elif flag == 2:
        num = len('Python-')
        new_name = name[num:]

# 打印新⽂件名，测试程序正确性
print(new_name)
 
# 重命名
os.rename(dir_name+name, dir_name+new_name)
```