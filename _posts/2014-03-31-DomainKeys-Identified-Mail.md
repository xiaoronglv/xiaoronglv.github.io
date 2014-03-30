---
title: DomainKeys Identified Mail
layout: post
guid: QjbM2IOF57zh
tags:
   - EDM
---

DKMI 和 SPF 是 EDM 最常用的两个验证方法。

DomainKeys Identified Mail 也是利用 DNS 来反垃圾邮件。

![DKIM]({{ site.url }}/media/files/2014/mar/31-DKIM.jpg)

## 前提

* 域名的持有者将公钥放到 DNS 上
* 发送者发送的邮件都用私钥生成 digest

## 流程

1. 发件服务器发送的邮件，用私钥生成加密的 digest
2. 收件服务器收到邮件
    * 查询 DNS，获取公钥
    * 公钥解密，比对 digest
3. 如果一致，代表发件靠谱；如果不一致：发件人是伪造的。
