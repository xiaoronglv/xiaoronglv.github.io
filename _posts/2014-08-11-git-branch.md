---
title: Git 分支的原理
layout: post
guid: PKJ2OriwfFvq
date: 2014-08-11 16:00:00
tags:
   - Git
---

> 阅读本文之前，请掌握三个概念：Commit、Tree、Blob。[《》]()


听前辈们说， SVN 有诸多坏处坏处，非分布式，提交代码麻烦，创建、销毁、合并分支不方便。很多团队的代码管理已经从 SVN 转为 Git。

幸运的是我的职业生涯生涯，就没用过 SVN，直接从 Git 起步。

Git 切分支的成本低，速度快，更轻量，一个分支仅 40 字节。

    git checkout -b feature/drag


为什么40个字节就可以创建一个分支？

Git 管理分支的原理是什么？

一切的秘密都隐藏在在 .git 这个目录中。这个目录中的文件囊括所有的信息，包括分支，Commit 对象，文件内容（Blog），目录（Tree）， Tag。

## Git 如何管理 branch 呢？

    /Users/xiaoronglv/Repositories/xiaoronglv.github.io
    > cd .git

refs 这个目录用于保存分支信息，其下又有三个目录：

1. heads 保存本地的分支
2. remotes 保存远程仓库的分支
3. tags 保存所有的 tag

挑选 master 分支，在终端打印出来，发现只不过是串文本，指向了一个 Commit 对象。

![Branch]({{ site.url }}/media/files/2014/Aug/11-branch.png)


当我们使用 `git checkout -b feature/drag` 创建分支时，git 在 heads 下创建了 `feature/drag` 文件，并把 commit 对象的 SHA 值赋进去，就完成了。

分支的实现代价太低了，成百上千个分支也不会爆掉仓库。

![Branch Mindmap]({{ site.url }}/media/files/2014/Aug/11-branch-mindmap.png) 

## 当切换分支时，Git 做了什么？

在 .git 目录中，还有一个很好玩的文件 —— HEAD。当我们切换分支时， .git/HEAD 的指针也随之更改。


    # 切换至 master 分支
    /Users/xiaoronglv/Repositories/xiaoronglv.github.io
    > git checkout master
    > cat .git/HEAD
    ref: refs/heads/master
    
    # 切换至 feature/drag 
    /Users/xiaoronglv/Repositories/xiaoronglv.github.io
    > git checkout feature/drag
    > cat .git/HEAD
    ref: refs/heads/feature/drag



## 总结

1. Git 把文件切割到 Commit 、Tree 、Blog 对象中，所有的内容像积木，可复用，随意搭配。

2. 分支是 40 个字节的文本，指向 Commit 对象。



