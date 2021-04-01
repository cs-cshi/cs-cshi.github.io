---
title: Hexo-NexT 分类多层级描述
date: 2021-04-01 07:30:00
tags: 
      - Hexo 博客
categories: 
- [网站建设, Hexo 博客]
---

1. 分类一般用法
    Hexo 中分类和标签有着明显的差别：分类具有顺序性和层次性，也就是说 Foo, Bar 不等于 Bar, Foo；而标签没有顺序和层次。
    <!--more-->
    ```yaml
    categories:
    - Diary
    tags:
    - PS3
    - Games
    ```
2. 父子分类
   如果您有过使用 WordPress 的经验，就很容易误解 Hexo 的分类方式。WordPress 支持对一篇文章设置多个分类，而且这些分类可以是同级的，也可以是父子分类。但是 Hexo 不支持指定多个同级分类。下面的指定方法：
   ```yaml
   categories:
   - Diary
   - Life
   ```
   会使分类Life成为Diary的子分类，而不是并列分类。因此，有必要为您的文章选择尽可能准确的分类。
3. 并列+子分类
   如果你需要为文章添加多个分类，可以尝试以下 list 中的方法。
   ```yaml
   categories:
   - [Diary, PlayStation]
   - [Diary, Games]
   - [Life]
   ```
   此时这篇文章同时包括三个分类： PlayStation 和 Games 分别都是父分类 Diary 的子分类，同时 Life 是一个没有子分类的分类。 
4. 分类页面优化
   我想要的是这种层次分明的效果：
   ![result](categories-result.png)
   但我设置完成后，实际的效果, 层次感不强。:
   ![ori](categories_ori.png)
 
   **问题解决**
   首先尝试了CodeHeap 的文章[Hexo+NexT博客归档/标签/分类页美化](https://jrbcode.gitee.io/posts/be9758cd.html),对分类栏进行了优化。发现只是增加了效果，分类的层次结构还是不清晰。
   ![categories](categories_1.png)
   
   然后修改 /themes/next/source/css/_common/components/pages/categories.styl 代码,将  .category-list-child 的 padding-left 属性改为 60px。
   ```
   .category-list-child {
     padding-left: 60px;
   }
   ```
   最终效果图：
   ![categories](categories_2.png)

——————————
**参考**
- [Hexo 官方文档](https://hexo.io/zh-cn/docs/front-matter)
- [Hexo+NexT博客归档/标签/分类页美化](https://jrbcode.gitee.io/posts/be9758cd.html)
- [Hexo多级类别的层级显示](http://yuchen-lea.github.io/2016-01-23-display-hexo-category-in-hierarchy/)
