---
title: Hexo-NexT 设置圆角
date: 2021-04-01 07:30:00
tags: 
      - Hexo 博客
categories: 
- [网站建设, Hexo 博客]
---

1. 在 blog_root/source/_data/variables.styl 中增加下列代码：
<!--more-->
```css
// 圆角设置
$border-radius-inner     = 20px;
$border-radius           = 20px;
```

1. 然后在 NexT 的配置文件 _config.yml 中取消 variables.styl 的注释：
```yaml
variable: source/_data/variables.styl
```

3. 问题描述
   侧边栏的圆角明显有一层阴影，圆角效果不明显
   ![problem](circle_problem.png)
4. 问题解决
   github issues 上也有人提过类似的问题[侧边栏顶栏无法显示为圆角](https://github.com/next-theme/hexo-theme-next/issues/51)，并且给出了回答。
   ![issues](issues_answer.png)
   回答中有提到黑色背景覆盖了圆角，所以我尝试了下将黑色改为透明，成功实现。具体是修改 /themes/next/source/css/_variables/Gemini.styl 内代码,将 $body-bg-color 赋值为透明 transparent。
```css
// Variables of Gemini scheme
// ==================================================

@import "Pisces.styl";

// Settings for some of the most global styles.
// --------------------------------------------------
$body-bg-color           = #eee;
// $body-bg-color           = transparent;
// Borders.
// --------------------------------------------------
$box-shadow-inner        = 0 2px 2px 0 rgba(0, 0, 0, .12), 0 3px 1px -2px rgba(0, 0, 0, .06), 0 1px 5px 0 rgba(0, 0, 0, .12);
$box-shadow              = 0 2px 2px 0 rgba(0, 0, 0, .12), 0 3px 1px -2px rgba(0, 0, 0, .06), 0 1px 5px 0 rgba(0, 0, 0, .12), 0 -1px .5px 0 rgba(0, 0, 0, .09);

// $border-radius-inner     = 20px;
// $border-radius           = 20px;

$border-radius-inner     = initial;
$border-radius           = initial;
// $border-radius-inner     = 0 0 3px 3px;
// $border-radius           = 3px;
```

   