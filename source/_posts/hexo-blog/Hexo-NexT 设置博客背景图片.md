---
title: Hexo-NexT 设置博客背景图片
date: 2021-04-01 00:30:00
tags: 
      - Hexo 博客
categories: 
- [网站建设, Hexo 博客]
---

## 1. 设置背景图片
将想要的背景图片放入 themes/next/source/images。打开 themes/next/source/css/ _custom/custom.styl 文件，这个是 Next 故意留给用户自己个性化定制一些样式的文件，添加以下代码即可。
<!--more-->
实际上我的版本里没有_custom，通过新建目录无法实现。我是在 themes/next/source/css/_schemes/Gemini/index.styl 中添加的代码，Gemini是我的 next 的布局样式。

在写这篇博客的过程中，又尝试了下将代码添加到 blog_root/source/_data/styles.style 下，设置成功。如果没有这个目录，直接新建即可。

```css
body {
    background:url(/images/yourbackground.jpg);
    background-repeat: no-repeat;
    background-attachment:fixed; //不重复
    background-size: cover;      //填充
    background-position:50% 50%;
}
```
- background:url 为图片路径，也可以直接使用链接。
- background-repeat：若果背景图片不能全屏，那么是否平铺显示，充满屏幕
- background-attachment：背景是否随着网页上下滚动而滚动，fixed 为固定
- background-size：图片展示大小，这里设置 100%，100% 的意义为：如果背景图片不能全屏，那么是否通过拉伸的方式将背景强制拉伸至全屏显示。


## 2. 博客内容透明化
NexT 主题的博客文章均是不透明的，这样读者就不能好好欣赏背景图片了，下面的方法可以使博客内容透明化：

在 blog_root/source/_data/styles.style  中添加以下内容：
```css
//博客内容透明化
//文章内容的透明度设置
.content-wrap {
  opacity: 0.9;
}

//侧边框的透明度设置
.sidebar {
  opacity: 0.9;
}

//菜单栏的透明度设置
.header-inner {
  background: rgba(255,255,255,0.9);
}

//搜索框（local-search）的透明度设置
.popup {
  opacity: 0.9;
}
```
注意：其中 header-inner 不能使用 opacity 进行配置。因为 header-inner 包含 header.swig 中的所有内容。若使用 opacity 进行配置，子结点会出很多问题。

## 3. 参考
- [Hexo-NexT 设置博客背景图片](https://tding.top/archives/761b6f4d.html)