---
title: "Mac 重启后自动 ssh-add 私钥"
layout: post
guid: JwPsyr3q335Yda232Jw
date: 2017-06-02 17:18:49
coffee: y
tags:
  - 
---

管理服务器有一些安全常识:

- 禁止 root 远程登录
- 禁止密码登录
- 使用 priviate / public key 作为默认的登录方式。
- 修改 sshd 端口

From Linode: [Securing your server](https://www.linode.com/docs/security/securing-your-server)

安全级别上去后，问题随之而来，有一堆的私钥需要管理。每次 Macbook 升级系统，需要一次次手工 ssh-add 私钥，特别心烦。


几年前有这种方法 `ssh-add -K privateKey` 可以一劳永逸的把私钥存到 keychain 中，重启后也不需要手动 ssh-add，非常方便。

**这种方式不管用了。**

![](/media/files/2017/2017-06-02-ssh-add.jpeg)


## 新的解决方法

**创建 Automator 任务加载私钥，然后放到开机启动中。**

1. 创建一个 Automator task.
		![](/media/files/2017/2017-06-02-step1.jpeg)

2. 在 task 中写一串 shell script
		![](/media/files/2017/2017-06-02-step2.jpeg)

3. 把 task 添加到 login items 中，每次系统重启后会执行这个 Automator task，自动 ssh-add 所有的私钥。
		![](/media/files/2017/2017-06-02-step3.jpeg)
		![](/media/files/2017/2017-06-02-step4.jpeg)


搞定！


