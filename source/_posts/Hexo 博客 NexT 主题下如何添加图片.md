---
title: Hexo 博客 NexT 主题下如何添加图片
date: 2021-03-13 09:47:57
tags: 
      - Hexo
      - 搭建环境
categories: Hexo 博客
---

先安装插件，再打开 hexo 的配置，最后按照指定格式放置和引用图片。
前面的摘要貌似不能为空，否则`more`标签无效。

<!--more-->

1. 安装插件，在 hexo 根目录下执行
```
npm install hexo-asset-image --save
```
2. 打开 hexo 的配置文件 `_config.yml`，将 `post_asset_folder`，把这个选项从`false`改成`true`

3. 打开 hexo 根目录下的 `/node_modules/hexo-asset-image/index.js`，将内容更换为下面的代码。（此步骤不知道是否真的有效，我没用此步骤也可以，可能遇到的问题不一样，建议执行此步骤的时候先备份 index.js 文件）
```
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
        var link = data.permalink;
    if(version.length > 0 && Number(version[0]) == 3)
       var beginPos = getPosition(link, '/', 1) + 1;
    else
       var beginPos = getPosition(link, '/', 3) + 1;
    // In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
    var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
        if ($(this).attr('src')){
            // For windows style path, we replace '\' to '/'.
            var src = $(this).attr('src').replace('\\', '/');
            if(!/http[s]*.*|\/\/.*/.test(src) &&
               !/^\s*\//.test(src)) {
              // For "about" page, the first part of "src" can't be removed.
              // In addition, to support multi-level local directory.
              var linkArray = link.split('/').filter(function(elem){
                return elem != '';
              });
              var srcArray = src.split('/').filter(function(elem){
                return elem != '' && elem != '.';
              });
              if(srcArray.length > 1)
                srcArray.shift();
              src = srcArray.join('/');
              $(this).attr('src', config.root + link + src);
              console.info&&console.info("update link as:-->"+config.root + link + src);
            }
        }else{
            console.info&&console.info("no src attr, skipped...");
            console.info&&console.info($(this));
        }
      });
      data[key] = $.html();
    }
  }
});
```
> 实际上安装了 `hexo-asset-image --save` 插件后，通过`hexo new post ***`指令新建博文，除了在 `~/source/_post/`目录下新建 .md 文件外，还会新建一个同名的文件夹，图片直接放这个文件夹即可。在生成静态页面的时候，hexo 会将 .md 文件和图片放在一个文件夹内。
> 如果不通过`hexo new post ***`指令新建博文，可以直接在`_post`目录下新建 .md （注意自己加头部），以及同名的文件夹，将图片放这文件夹下效果一样。

4. 插入图片。语法和 markdown 语法一致。
```
![]()  # [描述，不能为空，否则不会显示，md 下是可以不写的](图片完整名称)
# eg. ![test](test.jpg)，注意 test.jpg 是按要求放在与此 .md 文件同名的文件夹内。
```