---
title: SendCloud 试用手记
layout: post
guid: RYz1TBRgjugo
tags:
   - 
---

第一次看到薄荷网发送激活邮件的代码时，小伙伴当场就惊呆了。薄荷网注册了好多个企业级箱的子帐号，随机的用这些子帐号发送注册邮件。

noreply@boohee.com  
boohee@boohee.com  
service@boohee.com  
...  

每个帐号大概可以发送500封邮件/day， 每天几千封的发送量虽然满足需求，但缺点也显露无遗。

## 缺点

1. 无法满足日益增长的新用户注册量。难道再注册几十个子帐号，随机发送邮件？
2. 无法统计打开率；
3. 无法统计点击量；
4. 无法对无效邮箱做进一步的处理；
4. 无法 ...

总之，自己处理邮件是吃力不讨好的事情，不如把专业的事情交给专业的人，所以 Vincent 让我找一个靠谱的第三方邮件发送服务商。


## 邮件发送服务商

Mailgun 和 SendCloud 在 Ruby-China 圈子里口碑都不错。知乎也在同时使用这两家的服务， @Rio 在 [知乎是怎样发邮件的？](http://zhuanlan.zhihu.com/riobard/19582345) 这篇文章中详细的介绍了知乎邮件系统的架构。

Mailgun 是很棒的一家邮件发送服务商，但它是国外的公司，考虑到无耻的GFW，以及网速等方面的因素，最终放弃了它。

SendCloud 是搜狐公司的产品，产品风格几乎和 Mailgun 一模一样，开发文档相当齐全，它应该是国内数一数二的邮件服务商了。

如果你的客户主要分布在国外，建议使用 Mailgun。如果在国内，建议使用 SendCloud。

试用 SendCloud 一周后，爽歪了....

## 邮件分类

SendCloud 非常强硬的要求用户设置两个域名，一个发送触发类邮件（transactional email），一个发送营销类邮件（bulk email）。

* mailer.boohee.com（触发类邮件）
* newsletter.boohee.com（营销类邮件）

> 参考阅读：
> [Bulk, marketing, transactional. What's the difference between the types of emails?](https://www.mailjet.com/support/bulk-marketing-transactional-etc-what-s-the-difference-between-the-types-of-emails,112.htm)

## 子帐号

每个域名下可以设置多个子帐号。建议一个业务启用一个子帐号，分析数据时便于细分。

从而非常方便的统计每个业务每天的发送量、无效邮件、退信、被 ESPs 判断为垃圾邮件、垃圾邮件举报、打开率等等。

比如我就是这么干的。

1. transactional email

    postmaster@boohee-register.sendcloud.org（注册激活）  
    postmaster@boohee-notification.sendcloud.org（提醒）  
    postmaster@boohee-recommand.sendcloud.org（个性化推荐）  
    postmaster@boohee-recall.sendcloud.org（唤醒老用户）  

2. bulk email
    
    postmaster@boohee-weeklydigest.sendcloud.org（每周精选）
  
<span class="image-1200">![子帐号]({{ site.url }}/media/files/2014/apr/12-sub-account.png)</span>

## 指标

SendCloud 提供了详尽的指标

发送数据

* 请求
* 发送
* 无效邮件
* 软退信
* 垃圾邮件
* 垃圾邮件举报
* 打开
* 点击
* 域名分布

一个喜欢用数据说话的人，见到这些曲线图和表格时的第一反映：指标真他妈的全。


## 溯源

当发送出现问题时，查看「投递回应」 可以非常方便的排查投递方面的原因：

1. 被 ESP 卡死了？（QQ邮箱经常限制新域名，每天500封）
2. 邮箱地址无效？
3. 被用户举报？
4. 被 ESP 判断为垃圾邮件？
5. 取消订阅？

<span class="image-1200">![投递回应]({{ site.url }}/media/files/2014/apr/12-response.png)</span>

## 退订、垃圾邮件管理

发送 EDM，以下是找死的行为：

* 被用户举报后，还持续的给用户发邮件；
* 被用户退订后，还持续的给用户发邮件；
* 持续的给错误的邮箱地址发送邮件。

这些行为会让 ESP 认为发送者是个 Spammer，降低信誉度。

为了避免这种悲剧，SendCloud 自动的将退订者、举报者、虚假地址加入到退订列表。不会继续给这些地址发送邮件，保护发送者的信誉度。

<span class="image-1200">![退订列表]({{ site.url }}/media/files/2014/apr/12-blacklist.png)</span>

## 高阶功能

1. Webhooks

    Webhook可在特定事件发生后，将消息推送到您设定的URL上
     
    当用户打开邮件、点击右键、取消订阅邮件、举报垃圾邮件时，SendCloud 可以回调请求我们的服务器，让我们有进一步的操作。

    比如：我们可以告诉用户「邮箱地址错误，请更换邮箱」。
    
2. 拉数据

    批量的导出无效邮件地址列表、举报者列表等等。
    
## 总结

SMTP 协议虽然简单，但 Email 是门大学问。自己搭建服务器折腾 Email 既浪费时间，又不专业。不如每个月花点小钱，交给专业的团队。

> 
> * 项目管理：[Tower](https://tower.im), [风车](https://fengcheco.com)
> * 企业办公邮箱：Google App
> * 邮件营销：SendCloud, Mailgun
> * 服务器：Linode, 阿里云
> * CDN：七牛, 又拍云
