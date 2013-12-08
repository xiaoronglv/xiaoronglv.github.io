---
title: when to use callback?
layout: post
guid: o8XAp2q56rFu
tags:
  - Rails
---

ActiveRecord 的 callback 是个很方便钩子，在 Model 的生命周期里可以帮我们做很多事情，比如：

1. 验证前后

    before_validation
    after_validation
    
2. 存储前后

    before_save
    after_save
    before_create
    after_create
    
3. 删除前后

    before_destroy
    after_destroy
 
Callback 真是 Rails 程序员的贴身小棉袄。

## 哪些逻辑最适合放到小棉袄中呢？

Callback 适合那些与 Model 十分紧密的逻辑

1. 计算值、默认值

    eg: 运动心率 = （220-年龄）x 60% 

    根据年龄计算出运动心率，并保存为 heart_rate 的默认值。

2. 验证

## Callback 的好出

流程写在同一个 model 中，阅读容易

## Callback 的坏处

1. 违背了单一职责原则（single responsibility principle）

2. 如果调用多个 callback，一个出错，全盘皆输。

    所以要保证 callback 一定能被成功执行。 

## 考考你

新用户注册后，后台发送 say_hello 邮件。这种逻辑应该放到 controller 中，还是 callback 中？


## 参考资料

1. [Using callbacks for domain logic in Rails](http://stackoverflow.com/questions/11039023/pros-and-cons-of-using-callbacks-for-domain-logic-in-rails)

2. [Ruby On Rails API: Callback](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html)


