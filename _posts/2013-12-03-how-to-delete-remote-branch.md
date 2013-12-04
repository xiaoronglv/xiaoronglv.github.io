---
title: 理解记忆：删除远程分支
layout: post
guid: GnAohmHPaFEY
tags:
  - Git
---

删除远程分支，这是一个很简单的命令，我却经常记不住。为什么用 push，而不是 delete ？

<pre><code>
git push origin :branch_name
</code></pre>

## 聊一聊 push 的标准用法

<pre><code>
git push origin local_branch_name:remote_branch_name
</code></pre>

* local_branch_name：本地分支名字
* remote_branch_name：远程分支名字

这条命令的意思是，把本地的分支推送到远程分支。



<pre><code>
# 将本地的 master 分支推送至远程 master 分支
git push origin master:master 

# 当本地分支的名字与远程分支相同时，本地分支名和冒号可以忽略，可以简写为：
git push origin master 
</code></pre>

## 理解删除命令

熟悉 push 命令后，删除远程分支的命令的机制一目了然：用本地的一个空分支，替换远程的分支。

<pre><code>
git push origin :branch_name
</code></pre>

## 总结

1. 冒号前面一定要有一个空格，代表本地空分支。
2. 使用替换的方式来实现删除远程分支。

自从理解了push 命令，妈妈再也不担心我的记忆力了。