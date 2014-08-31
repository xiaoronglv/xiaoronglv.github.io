---
title: 修剪本地的远程分支
layout: post
guid: Algzsz8HZDaE
date: 2014-08-30 20:23:00
tags:
   - 
---

![prune branch]({{ site.url }}/media/files/2014/Aug/30-prune-branch.jpg)


有的时候，我们已经在 origin 将某个分支合并至 master，并在 origin 上删除了它。但是本地的仓库不会自动更新，依然保存着这些远程分支的「快照」，每次查看分支时都特别凌乱。

![pruned]({{ site.url }}/media/files/2014/Aug/30-pruned.png)

可以通过这条命令来修剪本地的远程分支镜像，砍掉已经不存在的远程分支镜像。


    git remote prune origin
    

![pruned]({{ site.url }}/media/files/2014/Aug/30-prune.png)



    prune  
    *[pru:n]  
    n. 洋李, 李子干, 深紫红色, 傻瓜  
    vt. 修剪, 砍掉, 删除  


