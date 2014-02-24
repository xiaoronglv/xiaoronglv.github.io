---
title: Rails Asset Organization
layout: post
guid: hP28NNEOZIPy
tags:
  - Rails
---

Rails Asset Pipeline 的作用就是把一堆css文件压缩为一个css，把一堆js压缩为一个js，减少访问页面时的 Http 请求。

## css/js 存放在哪？（乐高配件）

<span class="image-800">![directory]({{ site.url }}/media/files/2014/feb/25-directory.png)</span>

在Rails中，样式文件保存在以下两个文件目录：

1. app/assets
    
    应用的样式文件，比如文章、用户、评论的css/js

2. lib/assets

    外源的样式文件，比如videojs，jQuery插件

## manifest 文件（图纸）

在assets目录下，有一类声明文件（manifest），一般是 application.css 或 application.js。

他们存在的意义就是告诉应用：我包含哪些css文件。

<span class="image-800">![directory]({{ site.url }}/media/files/2014/feb/25-compile.png)</span>

## 编译成功（乐高机器人）

在生产环境中，Rails会把压缩后的文件放置到 public/assets 中。

1. 只有一个js
2. 只有一个css
3. 去除了空格、注释、换行等
4. 创建一份gzip版本的样式文件

用户发起请求时，直接扔给用户压缩后的文件既可。

## 参考资料

1. [Rails Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html)


