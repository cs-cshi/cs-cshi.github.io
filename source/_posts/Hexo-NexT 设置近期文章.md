---
title: Hexo-NexT 设置近期文章
date: 2021-04-01 00:30:00
tags: 
      - Hexo 博客
categories: 
- [网站建设, Hexo 博客]
---

1. 新建 source/_data/sidebar.njk 文件，内容如下：
```
{# RecentPosts #}
{%- if theme.recent_posts %}
  <div class="links-of-recent-posts motion-element">
    <div class="links-of-recent-posts-title">
      {%- if theme.recent_posts.icon %}
      <i class="{{ theme.recent_posts.icon }} fa-fw"></i>
      {%- endif %}
      {{ theme.recent_posts.title }}
    </div>
    <ul class="links-of-recent-posts-list">
      {%- set posts = site.posts.sort('date', 'desc').toArray() %}
      {%- for post in posts.slice('0', theme.recent_posts.max_count) %}
        <li class="links-of-recent-posts-item">
          {{ next_url(post.path, post.title, {title: post.path}) }}
        </li>
      {%- endfor %}
    </ul>
  </div>
{%- endif %}
```

2. 修改_config.next.yml，取消 custom_file_path 中的 sidebar 和 style 两个注释，并新增 recent_posts 内容。variable 应该是设置另外的功能时打开的，和此处无关。实际上我的 sidebar 原本是 source/_data/sidebar.swig, 直接改成 njk 的没问题。
```yml
custom_file_path:
  #head: source/_data/head.swig
  #header: source/_data/header.swig
  sidebar: source/_data/sidebar.njk
  #postMeta: source/_data/post-meta.swig
  #postBodyEnd: source/_data/post-body-end.swig
  #footer: source/_data/footer.swig
  #bodyEnd: source/_data/body-end.swig
  variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl

recent_posts:
# 块标题
  title: 最近文章
# 图标
  icon: fa fa-history
# 最多多少文章链接
  max_count: 5
```

3. 修改 source/_data/styles.styl 文件，文件不存在新建即可。添加如下代码：
```css
// 近期文章
.links-of-recent-posts
  font-size: 0.8125em
  margin-top: 10px

.links-of-recent-posts-title
  font-size: 1.03em
  font-weight: 600
  margin-top: 0

.links-of-recent-posts-list
  list-style: none
  margin: 0
  padding: 0
```
————————
**参考**
- [Hexo NexT 添加近期文章功能](https://weilining.github.io/925-1.html)