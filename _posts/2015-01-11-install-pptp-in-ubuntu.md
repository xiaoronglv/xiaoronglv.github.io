---
title: Ubuntu 一键部署 PPTP
layout: post
guid: m5ahni5gfdql
date: 2015-01-11 21:54:44
tags:
   -
---

![邮件提醒]({{ site.url }}/media/files/2015/Jan/2015-01-11-gfw.jpg)


GFW 真是无耻到家，丧心病狂，连 Gmail 都不放过。导致我们教会的好多兄弟姐妹无法正常沟通。

作为教会的 IT 支持，我想到两个解决方案：

1. 让他们去购买成熟的VPN 服务，比如云梯、曲径。

2. 自己搭建一个 PPTP server，供100人使用。


我们教会人数多，用户又比较善良，不会出现挂 VPN BT 下载这种行为，用第二种方案比较省钱。


## 1. 虚拟主机（VPS）

在 [Linode](https://www.linode.com) 购买低配套餐

* 10美元/月。
* Tokyo 机房
* Ubuntu 12.04 LTS 操作系统

比较悲催的是 Linode 东京机房的 ip 好多已被墙，我重复购买了5次，才找到一个没有被 GFW 屏蔽的 ip。

![邮件提醒]({{ site.url }}/media/files/2015/Jan/2015-01-11-linode.jpg)


## 2. 一键部署 PPTP server

在 Github 上有一个比较干净的 shell 脚本 [Github: Setup Simple PPTP VPN server for Ubuntu and Debian](https://github.com/viljoviitanen/setup-simple-pptp-vpn)，下载后使用 root 执行，就安装成功了。

真正的傻瓜式操作，一键安装。

如果对脚本安全性有疑问，你可以先看一下源码，再执行这个脚本。

```bash
wget https://raw.github.com/viljoviitanen/setup-simple-pptp-vpn/master/setup.sh
sudo sh setup.sh
```

## 配置账户

执行完毕后，再去这个文件配置一下用PPTP 用户和密码就可以了。

```
#/etc/ppp/chap-secrets
# Secrets for authentication using CHAP
# client server secret IP addresses
vpn pptpd fAj-JwC-rLu *
helloworld * 79lav7w59eeqeknp *
yangyihappy * 16ksqv4i2uk85m4a *
```

配置完账户，重启 pptp。

```bash
service pptpd restart
```

it works.




## 参考资料

1. [Github: Setup Simple PPTP VPN server for Ubuntu and Debian](https://github.com/viljoviitanen/setup-simple-pptp-vpn)


