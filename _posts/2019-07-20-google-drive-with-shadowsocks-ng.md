---
title: "Google Drive ❤️ 酸酸乳"
layout: post
guid: GmxKEuXiSDSN8rInblCc85US
date: 2019-07-20 11:26:38
coffee:
tags:
  -
---

![](/media/files/2019/2019-07-20-google-backup-sync-2.jpg)

我的家庭相册都是使用的 Google Photo 来备份， Goolge Drive 提供了一个客户端 [Google Drive Backup & Sync](https://www.google.com/drive/download/backup-and-sync/)，当有新的照片添加到电脑里时，就会自动同步到 Google Photos，非常方便。

但问题是传统的 PPTP 等代理已经不能工作了，现在只能通过 Shadowsocks 来翻墙，Google Backup and Sync 并不支持 Socket 代理，无法连接到服务器。我相信很多人会遇到类似的问题。


其实，你可以下载一个 ShadowsocksX-NG 客户端，同时开启它的 HTTP 代理，这样整个电脑就可以通过 HTTP 代理来翻墙了。Google Backup & Sync 也会正常工作了。


1. 下载 [Shadowsock-NG](https://github.com/shadowsocks/ShadowsocksX-NG)


2. 配置 Shadowsocks

	你需要自己购买一个 shadowsock 服务账号。


3. 开启 ShadowsocksX-NG 的 HTTP 代理

	![](/media/files/2019/2019-07-20-menu.jpg)

	![](/media/files/2019/2019-07-20_11-52-15-enable-http-proxy.jpg)


4. 把 Mac 的系统代理设置为第三步 ShadowsocksX-NG 的 HTTP 代理


	你可以按照这个路径 Network -> advanced -> Proxies 找到 Mac 的代理设置页面。先设置 HTTP 代理 （如①所示) ， 再设置 HTTPS 代理（如②所示）。

	![](/media/files/2019/2019-07-20_11-55-31-config.png)


现在你的 Google Backup & Sync 客户端就应该可以正常工作了。



## 参考资料

1. [Google Drive help: Why i can't use Backup&sync?](https://support.google.com/drive/forum/AAAAOxCWsToV2n2XQf-DYA/?hl=en&gpf=%23!topic%2Fdrive%2FV2n2XQf-DYA)

2. 网上有个比较复杂的解决方案：使用 [Proxifier](https://blog.batesma.com/20170811-google-drive-proxy-solved/), 为Google Drive 的 Backup & Sync 进行代理。这需要引入一个代理软件 Proxifier，无疑把普通小白搞的更加晕头转向。



