---
title: Concern 与 Service Object 的使用场景
layout: post
guid: A9gMtxwmEjsa
date: 2014-06-11 21:00:00
tags:
   - Ruby
---

![Fat man]({{ site.url }}/media/files/2014/Jun/11-fatman.jpg)

虽然大家都说一个健康的 Rails 项目应该「胖 Model，瘦 Controller」。但 Model 胖到几百行，惨无人睹。我最近正在尝试用 ActiveSupport::Concern 或 Service Object 将 Model 中的逻辑剥离处来，以下是我的使用心得。


## 什么情况下用 Service Object？


1. 某个方法内部需要多个 Model 通力合作，则这个方法需要放到 Service 中
  
    比如当订单发货后，发送邮件（OrderMailer.alert）、发送短信(SMS#send)、抓取快递信息(Shipment#fetch)
    
2. 某个方法依赖第三方服务

    比如通过接口抓取物流信息
    


## 什么情况下用 ActiveSupport::Concern


1. 多个 Model 具有相同的方法，则此方法应该剥离到 concern 中

    比如：Trashable, Searchable, Visible, Movable, Taggable



## 参考阅读

1. [7 Patterns to Refactor Fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)

2. [Put chubby models on a diet with concerns](http://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns)

3. [Railscast: Service Objects](http://railscasts.com/episodes/398-service-objects?view=comments)


