---
title: Concern 与 Service Object 的使用场景
layout: post
guid: A9gMtxwmEjsa
date: 2014-06-11 21:00:00
tags:
   - Ruby
---

![Fat man]({{ site.url }}/media/files/2014/Jun/11-fatman.jpg)

虽然大家都说一个健康的 Rails 项目应该「胖 Model，瘦 Controller」。但 Model 胖到几百行，也会惨无人睹。我最近正在尝试用 ActiveSupport::Concern 或 Service Object 将 Model 中的逻辑剥离处来，以下是我的使用心得。

## 哪些方法应该放到 Service Object？


1. 方法内部需要多个 Model 通力合作
  
    比如当订单发货后，
    
    step1 发送邮件(OrderMailer.alert)
        
    step2 发送短信(SMS#send)
    
    step3 抓取快递信息(Shipment#fetch)
    
2. 依赖第三方服务的方法

    比如通过接口抓取物流信息
    


## 哪些方法应该放到 ActiveSupport::Concern？

某方法被多个 Model 复用时，应该剥离到 Concern 中，比如：Trashable, Searchable, Visible, Movable, Taggable 等等。

其他的场景暂未想到...

## 参考阅读

1. [7 Patterns to Refactor Fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)

2. [Put chubby models on a diet with concerns](http://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns)

3. [Railscast: Service Objects](http://railscasts.com/episodes/398-service-objects?view=comments)


