---
title: 如何使用 Github + Hexo 搭建博客
date: 2021-03-13 09:47:57
tags: 
      - Hexo
      - 搭建环境
categories: Hexo 博客
---

> 搭建初衷：个人喜欢在本地用 markdown 来记录。之前使用过博客园、印象笔记等，发现都有不便之处。博客园上在发文时，还需要来调整格式，且不能将本地的 md 文件直接上传，会增加很多工作；而在印象笔记上，不开会员会有诸多的限制，其中限制终端登录数量，以及不同终端不能编辑等问题无法接受。于是产生了一种需求:我只要将自己编写的 md 上传，就能形成良好的博客效果，格式固定,且便于维护和浏览,。在 Github Pages 上搭建博客基本能够满足我的需求。

> Github Pages 上搭建博客的弊端：
>   1. 部署在 Github 上，需要科学上网
>   2. 整个流程：撰写 md 文档 ——> Hexo 生成静态网页 ——> 部署到Github Pages ——>  浏览
>   3. 维护一个静态网站，需要有容灾和备份的意识和能力。

<!--more-->

# 搭建博客

## 1. 环境搭建
Hexo 基于 Node.js，搭建过程中还需要使用 npm（Node.js 已带） 和 git，因此先搭建本地操作环境，安装 Node.js 和 Git。
- Node.js：https://nodejs.org/zh-cn
- Git：https://git-scm.com/downloads

下载 Node.js 和 Git 程序并安装，一路点 “下一步” 按默认配置完成安装。

安装完成后，Win+R 输入 cmd 并打开，依次输入 node -v、npm -v 和 git --version 并回车，如下图出现程序版本号即可
![test](version.png)

## 2. 连接博客
使用邮箱注册 GitHub 账户，选择免费账户（Free），并完成邮件验证。

右键 -> Git Bash Here，**设置用户名和邮箱**：
> git config --global user.name "GitHub 用户名"
> git config --global user.email "GitHub 邮箱"

**创建 SSH 密匙**
> 输入 ssh-keygen -t rsa -C "GitHub 邮箱"，然后一路回车。

**添加密匙**：

进入 [C:\Users\用户名\.ssh] 目录（要显示隐藏文件，如果路径不对可以看上一步 `创建 SSH 密钥`时的输出内容，会有路径信息），打开公钥 id_rsa.pub 文件并复制里面的内容。

登陆 GitHub ，进入 Settings 页面，选择左边栏的 SSH and GPG keys，点击 New SSH key。

Title 随便取个名字，粘贴复制的 id_rsa.pub 内容到 Key 中，点击 Add SSH key 完成添加。

**验证连接**：
> 打开 Git Bash，输入 ssh -T git@github.com 出现 “Are you sure……”，输入 yes 回车确认

> 显示 “Hi xxx! You've successfully……” 即连接成功。

## 3. 创建 Github pages 仓库
GitHub 主页右上角加号 -> New repository：
- Repository name 中输入：用户名.github.io
- 勾选 Add a README file，会自动设置分支（分支名设置成master）：This will set  master as the default branch.
- create repository

## 4. 创建保存源码的分支
GitHub Pages 会自动部署静态网页文件，并将 master 分支作为部署的默认分支。为将静态网页和源文件（包含文章、主题等）分离开，强烈建议创建新分支，这样 master 分支只用来发布静态网页，而文档编辑和 Hexo 操作都在另一个分支上完成。

打开博客所在本地的目录，右键 -> Git Bash Here，将 git 仓库 clone 至本地：
> git clone https://github.com/用户名/用户名.github.io.git

cd 命令进入仓库目录,再创建本地分支：
> git checkout -b hexo-source     # hexo-source 是我的分支名

切换到新建分支(hexo-source是我的分支名)
> git checkout -b hexo-source 

查看本地分支(windows),此时 hexo-source 分支应该是高亮(当前分支)
> git branch -l

## 5. 本地安装 Hexo 博客程序
由于只能在空文件夹中生成 Hexo 项目,所以我们先将 `.git` 以及其他文件(如 `README.MD`)移出去,完成初始化后再移回来.

** 安装 Hexo **
> npm install -g hexo-cli

**Hexo 初始化和本地预览**
初始化并安装所需组件：
> hexo init      # 初始化
> npm install    # 安装组件

**启动本地服务器及进行预览**
> hexo g   # 生成页面
> hexo s   # 启动预览

访问 http://localhost:4000， 出现 Hexo 默认页面，本地博客安装成功！

## 6. 部署 Hexo 到 Github Pages
本地博客测试成功后，就是上传到 GitHub 进行部署，使其能够在网络上访问。

首先安装 hexo-deployer-git：
> npm install hexo-deployer-git --save

然后修改 _config.yml 文件末尾的 Deployment 部分，修改成如下：
```
deploy:
  type: git
  repo: git@github.com:用户名/用户名.github.io.git
  branch: master
```

执行 `hexo g -d`部署静态页面至 Github Pages.

如果成功,此时通过`https://cs-cshi.github.io/` 会出现 Hexo 默认页面.

## 7. 部署 源文件到 Github Pages
> 突然发现 github pages 仓库中没有 hexo-source 分支,才想起还没有将本地 git pull 到 github 上,按理说此步骤可以提前至创建分支那一步.

先查看本地分支和远程仓库分支,发现本地和远程不一致,本地存在我创建的 hexo-source 分支
> git branch -a

将本地创建的分支 push 到 github 仓库,两个 hexo-source,一个是本地名,一个是远程仓库里的命名.
> git push origin hexo-source:hexo-source

由于有部分是 Hexo 初始化的文件,不需要上传,可以过滤掉.打开 `.gitignore`文件,选择 过滤的文件
```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

将本地的源文件 push 到 github 仓库.
```
git add .
git commit -m 'hexo source post' # 引号内是描述
git push origin hexo-source  # hexo-source 是分支名
```

注意：如果是通过 git clone 下载配置的主题， push 源文件时需要将主题的 `.git`文件夹删除或改名备份。


## 8. 更换主题
在 Themes | Hexo 选择一个喜欢的主题，比如 NexT，进入网站目录打开 Git Bash Here 下载主题：
> 主题链接: https://hexo.io/themes/
> 我所用的  NexT主题说明文档: http://theme-next.iissnan.com/getting-started.html

## 9. 发布文章
进入博客所在目录，右键打开 Git Bash Here，创建博文：
> hexo new "My New Post"

然后 source 文件夹中会出现一个 My New Post.md 文件，就可以使用 Markdown 编辑器在该文件中撰写文章了。

写完后运行下面代码将文章渲染并部署到 GitHub Pages 上完成发布。以后每次发布文章都是这两条命令。
> hexo g   # 生成页面
> hexo d   # 部署发布

也可以不使用命令自己创建 .md 文件，只需在文件开头手动加入如下格式 Front-matter 即可，写完后运行 hexo g 和 hexo d 发布。
```
---
title: Hello World # 标题
date: 2019/3/26 hh:mm:ss # 时间
categories: # 分类
- Diary
tags: # 标签
- PS3
- Games
---

摘要
<!--more-->
正文
```

## 10. 常见命令
```
hexo new "name"       # 新建文章
hexo new page "name"  # 新建页面
hexo g                # 生成页面
hexo d                # 部署
hexo g -d             # 生成页面并部署
hexo s                # 本地预览
hexo clean            # 清除缓存和已生成的静态文件
hexo help             # 帮助
```

## 参考
- [使用 Hexo+GitHub 搭建个人免费博客教程（小白向）](https://zhuanlan.zhihu.com/p/60578464#:~:text=%E4%BD%BF%E7%94%A8%20Hexo%2BGitHub%20%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%85%8D%E8%B4%B9%E5%8D%9A%E5%AE%A2%E6%95%99%E7%A8%8B%EF%BC%88%E5%B0%8F%E7%99%BD%E5%90%91%EF%BC%89%201%20%E5%87%86%E5%A4%87%202%20%E8%BF%9E%E6%8E%A5%20Github....,Hexo%20%E5%88%B0%20GitHub%20Pages%206%20%E7%BB%91%E5%AE%9A%E5%9F%9F%E5%90%8D%EF%BC%88%E5%8F%AF%E9%80%89%EF%BC%89....%207%20%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8)
- [ s1mplecc 的 ghost-to-hexo-migrater 项目](https://github.com/s1mplecc/ghost-to-hexo-migrater)
- [使用git分支保存hexo博客源码到github](https://www.jianshu.com/p/8814ce1da7a4)