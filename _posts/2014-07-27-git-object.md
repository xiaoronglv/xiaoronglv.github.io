---
title: Git 的存储对象
layout: post
guid: gkZqC3I84lCK
date: 2014-07-27 15:00:00
tags:
   - Git
---

![Git Object]({{ site.url }}/media/files/2014/jul/29-git.png)


去年读 Git Pro 这本书时，很难理解 Git 的四种对象模型 Blob、Tree、Commit、Tag，就直接跳过了。

今年用 Git 逐渐多了起来，且有了一丁点 Linux 基础，回头再看豁然开朗。Git 的存储方式真是既精妙、又简单。

## Blob 对象

新文件纳入到 Git 后会被五马分尸，它的内容被扔到在一个 blob 对象中，它的对象名是基于内容运算生成的一个 40个字符的 SHA1值。

blob 没有文件名，只有内容。

![Git Commit]({{ site.url }}/media/files/2014/jul/29-object-blob.png)


## Tree 对象

一个 tree 对象就是一大坨指针，指向：

1. 其他 small tree（子级 tree）
2. blob

可以把 Tree 对象想象为 Linux 文件系统中的目录，记录了子目录的信息、文件信息。


![Git Commit]({{ site.url }}/media/files/2014/jul/29-object-tree.png)


![Git Commit]({{ site.url }}/media/files/2014/jul/29-2-tree.png)


## Commit 对象

一个 commit 对象由以下几部分组成

1. 作者
2. 提交者
3. 注释
4. 指向一个  big tree 的指针

![Git Commit]({{ site.url }}/media/files/2014/jul/29-object-commit.png)


还有一个 tag 对象，平时几乎不用，就先不写了。

## 参考资料

[Git Community Book：Git 对象模型](http://gitbook.liuhui998.com/1_2.html)