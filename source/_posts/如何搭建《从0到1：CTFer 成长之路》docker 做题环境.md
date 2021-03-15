---
title: 如何搭建《从0到1：CTFer 成长之路》docker 做题环境
date: 2021-03-15 10:18:57
tags: 
   - 网络安全
   - 搭建环境
categories: 《从0到1：CTFer成长之路 》
---

《从0到1：CTFer 成长之路》书中涉及的题目已经封装成 docker 镜像，通过 docker 很容易能在本地实现做题的环境。

<!--more-->

## 1. 安装 docker 
- 使用官方安装脚本安装
  ```
  sudo curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
  ```
- 也可以使用国内 daocloud 一键安装命令：
  ```
  sudo curl -sSL https://get.daocloud.io/docker | sh
  ```
  详细文档：https://www.runoob.com/docker/ubuntu-docker-install.html
  > 由于之前使用了 snap 安装过 docker，运行 docker 命令出现：
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; bash:/snap/bin/docker: No such file or directory
  > 而实际上使用官方安装脚本安装后的 docker 应该链接至：/usr/bin/docker
  > 因此将 /snap/bin/docker 链接至 /usr/bin/docker 即可
- 查看 docker 镜像
  ```
  sudo docker images
  ```
- 重启 docker 服务
  ```
  systemctl daemon-reload
  systemctl restart docker
  ```

## 2. docker 换源
- docker换源的配置文件默认在`/etc/docker/daemon.json`，若无 `daemon.json`，直接创建即可
  ```
  cd /etc/docker
  # 若该目录下无 daemon.json，直接创建
  sudo touch daemon.json

  sudo vim daemon.json
  # 将下面内容输入到 daemon.json 中
  ```
  ```
  {
    "registry-mirrors" : [
      "http://ovfftd6p.mirror.aliyuncs.com",
      "http://registry.docker-cn.com",
      "http://docker.mirrors.ustc.edu.cn",
      "http://hub-mirror.c.163.com"
    ],
    "insecure-registries" : [
      "registry.docker-cn.com",
      "docker.mirrors.ustc.edu.cn"
    ],
    "debug" : true,
    "experimental" : true
  }
  ```
- 保存并重启 docker 服务
  确定是否换源成功，如果如下命令能看到 daemon.json 里的网址表示换源成功
  
  ```
  systemctl daemon-reload
  systemctl restart docker
  ```
  ```
  sudo docker info
  ```

## 3. 安装 docker-compose
> docker-compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

- 从 Github 安装
   使用 curl 下载 1.27.4版本，如果需要安装其他版本，修改版本号即可。
  ```
  curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  ```
- 增加执行权限
  ```
  chmod +x /usr/local/bin/docker-compose
  ```
- 创建软链接
  ```
  ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
  ```
- 测试是否安装成功
  ```
  # sudo docker-compose --version
  docker-compose --version
  ```
## 4. 配置具体题目环境 docker-compose.yml
- 在 home/用户 下 创建一个测试目录 compose
  ```
  mkdir compose
  ```
- 配置 docker-compose.yml，若不存在此文件，直接新建
  ```
  # yaml 配置:SQL 注入-1
  version: '3.2'
  services:
    web:
      image: registry.cn-hangzhou.aliyuncs.com/n1book/web-information-backk:latest
      ports:
          - 80:80
  ```

## 5. 启动方式
在当前题目配置 docker-compose.yml 所在文件夹，执行以下命令
```
docker-compose up -d

or 

sudo docker-compose up -d
```
可以对每个题目都创建一个文件夹，里面存放这个题目的 docker-compose.yml，这样需要启动某个题目环境的时候直接进它的目录执行启动指令即可。