---
title: Git 分支的原理
layout: post
guid: PKJ2OriwfFvq
date: 2014-08-11 16:00:00
tags:
   - Git
---

> 阅读本文之前，请先掌握三个概念：Commit、Tree、Blob，详见[《Git 的存储对象》](http://mednoter.com/git-object.html)。

    git checkout -b feature/drag

我们使用以上命令可以轻松创建一个分支，在这背后Git 都做了什么呢。

    /Users/xiaoronglv/Repositories/xiaoronglv.github.io
    > cd .git

.git/refs 目录下有三个子目录：

1. heads 保存本地的分支
2. remotes 保存远程仓库的分支
3. tags 保存所有的 tag

在终端把 Master打印出来，发现只不过是串文本，指向了一个 Commit 对象。

![Branch]({{ site.url }}/media/files/2014/Aug/11-branch.png)


当我们使用 `git checkout -b feature/drag` 命令时，git 在 heads 下创建了 `feature/drag` 文件，并把 commit 对象的 SHA 值赋进去，一个分支就创建成功了。


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



