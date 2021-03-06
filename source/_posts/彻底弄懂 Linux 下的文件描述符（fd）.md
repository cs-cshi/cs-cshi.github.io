---
title: 彻底弄懂 Linux 下的文件描述符（fd）
date: 2021-03-23 09:22:57
tags: 
   - linux
categories: 
- linux
---

Linux 下的文件描述符

<!--more-->

## 1. 从一个最常见的例子说起
在使用Linux的过程中， 我们平时经常看到下面这样的用法：

> echo log > /dev/null 2>&1

- >  ：表示将输出结果重定向到哪里，例如：echo "123" > /home/123.txt
- /dev/null ：表示空设备文件
  所以 echo log > /dev/null 表示把日志输出到空文件设备，也就是将打印信息丢弃掉，屏幕上什么也不显示。
- 1  ：表示stdout标准输出
- 2  ：表示stderr标准错误
- &  ：表示等同于的意思

所以  2>&1 表示2的输出重定向等同于1，也就是标准错误输出重定向到标准输出。因为前面标准输出已经重定向到了空设备文件，所以标准错误输出也重定向到空设备文件。

这个用法平时很常见，重点是为什么这里是用 2 和 1 ，不是3456什么的呢？这要从 Linux 中的文件描述符说起。

## 2. Linux中的文件描述符（file descriptor）
我们知道在Linux系统中一切皆可以看成是文件，文件又可分为：普通文件、目录文件、链接文件和设备文件。在操作这些所谓的文件的时候，我们每操作一次就找一次名字，这会耗费大量的时间和效率。所以Linux中规定每一个文件对应一个索引，这样要操作文件的时候，我们直接找到索引就可以对其进行操作了。

文件描述符（file descriptor）就是内核为了高效管理这些已经被打开的文件所创建的索引，其是一个非负整数（通常是小整数），用于指代被打开的文件，所有执行I/O操作的系统调用都通过文件描述符来实现。同时还规定系统刚刚启动的时候，0是标准输入，1是标准输出，2是标准错误。这意味着如果此时去打开一个新的文件，它的文件描述符会是3，再打开一个文件文件描述符就是4......

Linux内核对所有打开的文件有一个文件描述符表格，里面存储了每个文件描述符作为索引与一个打开文件相对应的关系，简单理解就是下图这样一个数组，文件描述符（索引）就是文件描述符表这个数组的下标，数组的内容就是指向一个个打开的文件的指针。

![file descriptor](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/linux/fd_file_descriptor.png)

**上面只是简单理解，实际上关于文件描述符，Linux内核维护了3个数据结构：**
- 进程级的文件描述符表
- 系统级的打开文件描述符表
- 文件系统的i-node表

一个 Linux 进程启动后，会在内核空间中创建一个 PCB(Process Control Block) 控制块，PCB 内部有一个文件描述符表（File descriptor table），记录着当前进程所有可用的文件描述符，也即当前进程所有打开的文件。进程级的描述符表的每一条记录了单个进程所使用的文件描述符的相关信息，进程之间相互独立，一个进程使用了文件描述符3，另一个进程也可以用3。除了进程级的文件描述符表，系统还需要维护另外两张表：打开文件表、i-node 表。这两张表存储了每个打开文件的打开文件句柄（open file handle）。一个打开文件句柄存储了与一个打开文件相关的全部信息。

**系统级的打开文件描述符表：**
- 当前文件偏移量（调用read()和write()时更新，或使用lseek()直接修改）
- 打开文件时的标识（open()的flags参数）
- 文件访问模式（如调用open()时所设置的只读模式、只写模式或读写模式）
- 与信号驱动相关的设置
- 对该文件i-node对象的引用，即i-node 表指针

**文件系统的i-node表：**
- 文件类型（例如：常规文件、套接字或FIFO）和访问权限
- 一个指针，指向该文件所持有的锁列表
- 文件的各种属性，包括文件大小以及与不同类型操作相关的时间戳
- 文件描述符、打开的文件句柄以及i-node之间的关系如下图：
  ![relationship](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/linux/fd_relationship.png)

- 在进程 A 中，文件描述符 1 和 20 都指向了同一个打开文件表项，标号为 23（指向了打开文件表中下标为 23 的数组元素），这可能是通过调用 dup()、dup2()、fcntl() 或者对同一个文件多次调用了 open() 函数形成的。
- 进程 A 的文件描述符 2 和进程 B 的文件描述符 2 都指向了同一个文件，这可能是在调用 fork() 后出现的（即进程 A、B 是父子进程关系），或者是不同的进程独自去调用 open() 函数打开了同一个文件，此时进程内部的描述符正好分配到与其他进程打开该文件的描述符一样。
- 进程 A 的描述符 0 和进程 B 的描述符 3 分别指向不同的打开文件表项，但这些表项均指向 i-node 表的同一个条目（标号为 1976）；换言之，它们指向了同一个文件。发生这种情况是因为每个进程各自对同一个文件发起了 open() 调用。同一个进程两次打开同一个文件，也会发生类似情况。 

**这就说明：同一个进程的不同文件描述符可以指向同一个文件；不同进程可以拥有相同的文件描述符；不同进程的同一个文件描述符可以指向不同的文件（一般也是这样，除了 0、1、2 这三个特殊的文件）；不同进程的不同文件描述符也可以指向同一个文件。**

## 3. Linux上打开文件举例
比如在Linux上用 vim test.py 打开一个文件，保持打开状态，再新打开一个新的shell，输入命令pidof vim 获取vim进程的pid号，然后 ll  /proc/$pid/fd 查看vim 进程所使用的文件描述符列表。
![example vim](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/linux/fd_example_vim.png)

/dev/pts是远程登陆(telnet,ssh等)后创建的控制台设备文件所在的目录。因为我是通过Xshell远程登录的，所以标准输入0，标准输出1，标准错误2的文件描述符都指向虚拟终端控制台 /dev/pts/6 。再看下面是新打开的 test.py 的文件描述符，竟然是4，说好的从3开始呢？

这个我也困扰了好久，查了各种资料，终于在一个大佬的帮助下在一个论坛找到原因，有时候中文查不到还是要试试英文搜索啊。因为vim这种编辑器的原理是先打开源文件并拷贝，然后关闭源文件再打开自己的副本，修改完文件保存的时候直接将副本重命名覆盖源文件。所以打开源文件的时候用的文件描述符3，然后打开自己的副本是时候就该用文件描述符4了，然后关闭源文件，文件描述符3就被释放了，我们查看的时候就只剩下了4，这里它指向的是vim创建的副本文件。这里只是说个大概意思，具体深究要去深入了解一下[vim的实现原理——奥尔特星云大使](https://blog.csdn.net/weixin_43535689/article/details/104750148)，下面是当时我看到的论坛上的资料截图，链接在这：[StackOverFlow](https://stackoverflow.com/questions/48588284/lsof-command-can-not-return-files-being-opened-by-kwrite)。
![stackoverflow vim](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/linux/fd_stackoverflow_vim.png)

## 4. C语言中文件描述符的使用
C语言中可以通过 open 函数返回一个文件的文件描述符，首先创建一个 test.py 文件用于打开，然后创建一个 test.c 文件，输入下面代码保存。 编译后执行，发现新打开文件的文件描述符是3。
```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
 
int main(int argc, char* argv[]) {
        int fd = open("test.py", O_RDONLY);
        if (fd == -1) {
                return -1;
        }
        printf("test.py fd = %d \n", fd);  // test.py fd = 3
        close(fd);
        return 0;
}
```

## 5. Python中文件描述符的使用
Python中通过 sys 模块封装了标准输入、标准输出和错误输出。通过我们平时常用的内建函数 open 可以获取一个文件的文件描述符，首先创建一个 test.py 文件用于打开，然后创建一个 test2.py 文件，输入下面代码保存。 执行，发现新打开文件的文件描述符是3。
```python
import sys
 
print('stdin fd = ', sys.stdin.fileno())  // stdin fd = 0 
print('stdout fd = ', sys.stdout.fileno())  // stdout fd = 1
print('stderr fd = ', sys.stderr.fileno())  // stderr fd = 2
 
with open("test.py", "w") as f:
    print('test.py fd = ', f.fileno())  // test.py fd = 3
```

## 6. Linux配置系统最大打开文件描述符个数
### 系统级限制
理论上系统内存有多少就可以打开多少的文件描述符，但是在实际中内核是会做相应的处理，一般最大打开文件数会是系统内存的10%（以KB来计算），称之为系统级限制。这个数字可以通过 cat /proc/sys/fs/file-max 或者 sysctl -a | grep fs.file-max 命令查看。

更改系统级限制有临时更改和永久更改两种方式：
- 临时更改：session断开或者系统重启后会恢复原来的设置值。使用命令 sysctl -w fs.file-max=xxxx，其中xxxx就是要设置的数字。
- 永久更改：vim编辑 /etc/sysctl.conf 文件，在后面添加 fs.file-max=xxxx，其中xxxx就是要设置的数字。保存退出后还要使用sysctl -p 命令使其生效。

### 用户级限制
同时为了控制每个进程消耗的文件资源，内核也会对单个进程最大打开文件数做默认限制，即用户级限制。32位系统默认值一般是1024，64位系统默认值一般是65535，可以使用 ulimit -n 命令查看。

更改用户级限制也有临时更改和永久更改两种方式：
- 临时更改：session断开或者系统重启后会恢复原来的设置值。使用命令 ulimit -SHn xxxx 命令来修改，其中xxxx就是要设置的数字。
- 永久更改：vim编辑 /etc/security/limits.conf 文件，修改其中的 hard nofile xxxx 和 soft nofile xxxx，其中xxxx就是要设置的数字。保存后退出。关于hard和soft的区别，参照下面参考链接中的第5个。

## 7. 参考链接
1. [每天进步一点点——Linux中的文件描述符与打开文件之间的关系——cywosp](https://blog.csdn.net/cywosp/article/details/38965239)
2. [Linux文件描述符到底是什么？——C语言中文网](http://c.biancheng.net/view/3066.html)
3. [句柄和文件描述符（FD）——阳光丶不锈](https://www.jianshu.com/p/0ff9ff1d108e)
4. [带你破案：文件描述符到底是什么？——vran](https://juejin.cn/post/6844903962043236365#heading-0)
5. [Linux配置调优：最大打开文件描述符个数——Idea Buffer](http://www.ideabuffer.cn/2016/11/20/Linux%E9%85%8D%E7%BD%AE%E8%B0%83%E4%BC%98%EF%BC%9A%E6%9C%80%E5%A4%A7%E6%89%93%E5%BC%80%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6%E4%B8%AA%E6%95%B0/)
6. [修改Linux系统下的最大文件描述符限制——BlueguyChui](修改Linux系统下的最大文件描述符限制)

————————————————
版权声明：本文为CSDN博主「杰克小麻雀」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yushuaigee/article/details/107883964