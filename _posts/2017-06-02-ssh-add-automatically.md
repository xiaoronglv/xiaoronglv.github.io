---
title: "Mac 重启后自动 ssh-add 私钥"
layout: post
guid: JwPsyr3q335Yda232Jw
date: 2017-06-02 17:18:49
coffee: y
tags:
  - 
---

## 老方法不管用了

![](/media/files/2017-06-02-ssh-add.jpeg)

以前这种方法 `ssh-add -K privateKey` 可以一劳永逸的把私钥存到 到 keychain 中，重启后也不需要手动 ssh-add，非常方便。

系统升级后，这种方式不管用了。

## 新的解决方法: Automator

1. 创建一个 Automator task.
		![](/media/files/2017-06-02-step1.jpeg)

2. 在 task 中写一串 shell script
		![](/media/files/2017-06-02-step2.jpeg)

3. 把 task 添加到 login items 中，每次系统重启后会执行这个 Automator task，自动 ssh-add 所有的私钥。
		![](/media/files/2017-06-02-step3.jpeg)
		![](/media/files/2017-06-02-step4.jpeg)


搞定！


