---
title:  EDM: Sender Policy Framework
layout: post
guid: sOS9htjvY3Oi
tags:
   - EDM
   
---

SMTP 是一个比较古老的协议，在创造之初并没有考虑安全性与防伪造。Sender Policy Framework（SPF）是一个基于 IP 地址的防伪造方法。

## SPF 的机制

![Dropbox]({{ site.url }}/media/files/2014/mar/30_spf_flow.png)

1. Mednoter Server（发件服务器）发送邮件 
    
    from xiaoronglv@mednoter.com  
    to  angela@hotmail.com
    
2. Hotmail Server（收件服务器）查看验证发件人的 ip

    * 去 mednoter.com 的  DNS 上搜索 spf 记录
    * 拉取 SPF 记录中所有的IP列表 
    
3. 查看发件人的 IP 是不是在这个列表上
    
    * 在列表上：该发件服务器是靠谱的发件人
    * 不在列表上：该发件服务器是伪造的
    
## SFP 记录的语法

![Dropbox]({{ site.url }}/media/files/2014/mar/30_spf_syntax.png)

    v=spf1 mx a:mailer.example.net ip4:192.168.0.1/16 ip6:1080::8:800:200C:417A/96 include:spf.mednoter.com -all
    
1. v=spf1这说明这是 SPF 第一版

2. mx: 把域名上的 MX 记录所对应的服务器 IP 加入「信任列表」。

3. a: 把 mailer.example.net 这个域名 A 记录所对应的 IP 加入「信任列表」。

4. ip4:192.168.0.1/16代表把这一段 IPv4 的地址段加入「信任列表」。

5. ip6:1080::8:800:200C:417A/96代表把这一段 IPv6 的地址段加入「信任列表」。

6. include:aspmx.googlemail.com代表读取这个域名上的 SPF，然后把它们都加入「信任列表」。

7. -all 或 ~all 或 ?all这一项是用来处理不在「信任列表」里的 IP 地址的。

    * 减号，代表 hard fail，
    * 波浪线，代表 soft fail
    * 问号，代表「你看着办」。


