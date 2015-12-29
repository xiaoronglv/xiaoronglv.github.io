---
title: 让 Git 全局性的忽略 .DS_Store
layout: post
guid: 626f8872846a4c62a9d5392fa297339f
date: 2015-03-22 16:52:04
tags:
  - Git
---

Mac 中每个目录都会有个文件叫`.DS_Store`, 用于存储当前文件夹的一些 Meta 信息。每次提交代码时，我都要在代码仓库的 .gitignore 中声明，忽略这类文件。有方法可以全局性的忽略某种类型的文件吗？

按照以下两步就可实现

1. 创建 `~/.gitignore_global` 文件，把需要全局忽略的文件类型塞到这个文件里。

    ```
    # .gitignore_global
    ####################################
    ######## OS generated files ########
    ####################################
    .DS_Store
    .DS_Store?
    *.swp
    ._*
    .Spotlight-V100
    .Trashes
    Icon?
    ehthumbs.db
    Thumbs.db
    ####################################
    ############# Packages #############
    ####################################
    *.7z
    *.dmg
    *.gz
    *.iso
    *.jar
    *.rar
    *.tar
    *.zip
    ```

2. 在 ~/.gitconfig 中引入 `.gitignore_global`。

这是我的.gitconfig 文件:

```
[user]
	name = xiaoronglv
	email = xxxxx@gmail.com
[push]
	default = matching
[core]
	excludesfile = /Users/xiaoronglv/.gitignore_global
```

搞定了！在所有的文件夹下 .DS_Store .swp .zip 等文件类型会被 Git 自动忽略。

 ![demo](/media/files/2015-03-22-demo.png)

## 参考资料

 1. [Github: Create a global .gitignore](https://help.github.com/articles/ignoring-files/)

 2. [What is .DS_Store file?](http://osxdaily.com/2009/12/31/what-is-a-ds_store-file/)

 3. [.gitignore all the .DS_Store files in every folder and subfolder](http://stackoverflow.com/questions/18393498/gitignore-all-the-ds-store-files-in-every-folder-and-subfolder)
