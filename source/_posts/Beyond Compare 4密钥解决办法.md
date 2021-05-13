---
title: Beyond Compare 4密钥解决办法
date: 2021-04-15 19:27:00
tags: 
      - 软件资源
categories: 
- [工具, 软件]
---

Beyond Compare 4 密钥解决办法

<!--more-->

<article class="baidu_pl">
        <div id="article_content" class="article_content clearfix">
        <link rel="stylesheet" href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/ck_htmledit_views-b5506197d8.css">
                <div id="content_views" class="markdown_views prism-atom-one-dark">
                    <svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
                        <path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path>
                    </svg>
                    <p><strong>第一种办法（也是最有效的）</strong><br> <img src="https://img-blog.csdnimg.cn/20201020101129476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODM0NTMwNg==,size_16,color_FFFFFF,t_70#pic_center" alt="在这里插入图片描述"><br> 删除<strong>C:\Users\用户名\AppData\Roaming\Scooter Software\Beyond Compare 4</strong>下的所有文件，重启Beyond Compare 4即可（注意：用户名下的AppData文件夹有可能会被隐藏起来）</p> 
<p><strong>第二种办法</strong><br> 删除C:\Program Files\Beyond Compare 4\BCUnrar.dll（安装目录下的BCUnrar.dll文件），这个文件重命名或者直接删除。</p> 
<p><strong>第三种办法</strong><br> 修改注册表<br> 1、在搜索栏中输入 regedit ，打开注册表<br> 2、删除项目CacheId ：<br> HKEY_CURRENT_USER\Software\Scooter Software\Beyond Compare 4\CacheId</p>
                </div><div><div></div></div>
                <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/markdown_views-d7a94ec6ab.css" rel="stylesheet">
                <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/style-f1c5feb645.css" rel="stylesheet">
        </div>
    </article>

————————————
本文转载自：https://blog.csdn.net/weixin_38345306/article/details/109175632?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&dist_request_id=1331645.22594.16184845629740335&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control