---
title: 给私钥加把锁
layout: post
guid: OZtP9uIFoqLs
date: 2014-06-02 20:00:00
tags:
   - 
---

![Private Key]({{ site.url }}/media/files/2014/Jun/2-lock-and-key.png)

密钥登录是为懒人设计的，只要在电脑上保存一把私钥，就可以登录 Github、Gitlab、VPS 等等。曾几何时，我也认为这是又酷又安全又便捷的登录方式，直到 Vincent 告诉我：

> 我们如此依赖 Shell 脚本，一旦使用了不安全的脚本，就有可能把你的 private key 和 known hosts 上传给黑客。

> 所有依赖 SSH 登录的帐号系统全部沦陷，然后...就没有然后了...


## 私钥 & 密码

所以我们需要对私钥增加一套安全机制：在使用私钥时必须输入密码。假如黑客不知道你的私钥密码，那他也只不过偷了块废铁而已。


## 给已存在的私钥文件加密码

-p      

Requests changing the passphrase of a private key file instead of creating a new private key.  The program will prompt for the file containing the private key, for the old passphrase, and twice for the new passphrase.

    ssh-keygen -p -f keyfile
    
    
## OS X Keychain


![Private Key]({{ site.url }}/media/files/2014/Jun/2-ssh-prompt.jpg)

如果你是用的是 Mac 系统，可以通过以下命令把私钥和它的密码保存至 keychain，每次使用私钥时不用再输入这恼人的密码弹窗。

    ssh-add -K keyfile