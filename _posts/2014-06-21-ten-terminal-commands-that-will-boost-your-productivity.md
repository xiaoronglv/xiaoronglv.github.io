---
title: 提高生产力的10个终端命令
layout: post
guid: NP7bu1mFbqlI
date: 2014-06-21 15:00:00
tags:
   - Linux
---

> 用命令行的人有福了，因为终端是他们的；  
> 用命令的人有福了，因为他们必脱离鼠标。

以下是终端中常用的几个命令行，顺手整理了一下，美其名曰「提高生产力的十个终端命令」，希望对同学们有用。


## 1 Man

对某条命令不熟怎么办？  
忘记参数怎么办？

使用 man 可以非常方便查看每条命令的文档。

    $ man touch

![Man]({{ site.url }}/media/files/2014/Jun/21-man.png)

## 2 Touch

touch 本用于修改文件的 access 和 modification 时间，但程序猿们都喜欢用 touch 命令去创建一个空的文件。


    $ touch shared/production.log
    $ touch one.txt two.txt three.txt

![Touch]({{ site.url }}/media/files/2014/Jun/21-touch.png)

假如你想创建并编辑一个新文件，别用 touch，建议使用其他命令: 

    # textmate
    $ mate index.html

    # vim
    $ mvim index.html

    # sublime text
    $ subl index.html


    
## 3 Cat && Less

cat 和 less 都可在终端直接输出文件内容。我特别喜欢用来查看密钥，阅读只有十几行的小文本文件。

    $ cat shop_list.rb
    $ cat readme.md
    
![cat]({{ site.url }}/media/files/2014/Jun/21-cat.png)

但 cat 会输出文件**所有的内容**，假如你要查看的是服务器的日志，成百上万条记录瞬间刷爆你的屏幕，你就死定了！

此时建议你用 less 命令

    $ less production.log
    
他只会给你返回一屏的信息，你可以通过 jkbf 等快捷键来查阅该文件。

## 4 Tail

动态显示某个文件的最后几行，用来看日志是最合适不过的了。

    $ tail production.log

![tail]({{ site.url }}/media/files/2014/Jun/21-tail.png)


## 5 Curl

curl 最常见的应用场景有这么几个：

1. 查看网页源码

        curl www.sina.com


        <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
        <html><head>
        <title>301 Moved Permanently</title>
        </head><body>
        <h1>Moved Permanently</h1>
        <p>The document has moved <a href="http://www.sina.com.cn/">here</a>.</p>
        </body></html>


2. 加上 -o 参数可以下载文件
        
        curl -o [文件名] www.sina.com
        
        
3. 显示 HTTP response 的 head 信息

4. 提交表单数据。

5. .... 

curl 的用途太多，我就不一一赘述了， 它是 web 程序员必备命令，

阮一峰在[《curl 网站开发指南》](http://www.ruanyifeng.com/blog/2011/09/curl.html)中对该命令有更详尽的介绍，大家可以自行翻阅。


## 6 Gzip & Gunzip

在 Windows 中，压缩和打包归一个软件负责，不要用这种思维方式来理解 Linux 的压缩程序。

gzip 仅仅用于压缩文件，它才不管打包呢。Unix 的哲学是 **Do one thing well**。


    # 压缩文本文件 => hello.txt.gz
    $ gzip hello.txt

    # 压缩 tar 包 => hello.tar.gz
    $ gzip hello.tar.gz

    # 解压缩 => hello.txt
    $ gunzip hello.txt.gz

    # 解压缩 => hello.tar
    $ gunzip hello.tar.gz


## 7 Tar

Tar 命令用于打包/解包

想把 one.txt，two.txt 打包为一个文件？

    # -c : create, 打包（与之相对的是 -x，解包）
    # -f : 打包后的文件名
    $ tar cf archive.tar one.txt two.txt

想打包一个文件夹？

    # -c : create, 打包
    # -z : 打包后顺便用 gzip 压缩
    # -p : 保存相应的权限信息
    # -v : 把文件按打包顺序一一列出来
    # -f : 打包后的文件名
    $ tar czpvf archive.tar.gz document


gzip 命令和 tar 是截然不同的。

![what's the difference between tar and gzip]({{ site.url }}/media/files/2014/Jun/21-difference-between-tar-and-gzip.png)


## 8 History

可以通过 history 拉出来所有敲过的命令的历史记录。

    $ history
    
![History]({{ site.url }}/media/files/2014/Jun/21-history.png)


## 9 Chmod

chmod (change mode)用于修改文件的权限。

    $ chmod 644 authorized_keys

对权限系统不甚了解的同学可以去拜读 Rei 的大作《Linux 用户权限》。


## Read More

1. [10 Terminal Commands That Will Boost Your Productivity](http://code.tutsplus.com/articles/10-terminal-commands-that-will-boost-your-productivity--net-14105)

2. [curl 网站开发指南](http://www.ruanyifeng.com/blog/2011/09/curl.html)

3. [Rei: Linux 用户权限](http://chloerei.com/2012/08/26/rails-deploy-guides-2-linux-user-rights/)

就想到九个，还差一个命令，怎么办？