---
title: 电商系统促销模块的设计
layout: post
guid: B0se6A5eW3FW
tags:
   - Rails
---

> 至钱多人傻的妹子：
> 
> 我好羞愧，又骗你们花钱了。
> 
> 你是如此的单纯可爱，只要在商店的广告语上做个小小的修改（把文字调为红色），你们就会疯了般的购物。
> 
> 你们好伟大，养活了这么多人。
> 
> 吕小荣 敬上

本周的一个重要任务是设计电商系统的促销模块。在开始之前，我先梳理了商家常见的促销模式。


## 针对单品的促销规则

1. 直接折扣
    
    同一件商品，单价100， 特定的时间段折扣10元
    
    eg: 情人节、圣诞节、元宵节、光棍节

2. 秒杀

    同一件商品，特定的时间段 0元促销
    
    eg: 0元秒杀麦片，1元秒杀蜂蜜

3. 满件直接折扣（与针对订单的折扣区别不大，可忽略）

    就是说同一件商品，单价是80，客户同时购买30件，就打9.5折（即优惠 = 80 * (1 - 0.95) ），就是像批发一样的
    
    
## 针对订单的促销规则

1. 满额直接优惠
    
    满200，优惠30（即从200里减去30）

2. 满额直接折扣
    
    满500，打9.5折（即优惠 = 500 * (1 - 0.95) ）

3. 满额送优惠劵
    
    满1000，送你价值等值人民币200的优惠劵，下次购物，在买单时可以直接当人民币来使用。

4. 满额赠送物品
    
    满1000送橄榄油或蜂蜜，可以让客户自己在下单时候选择
    

## 设计思路 一

一开始我是按照以下思路开发优惠模块的。

在 products 表添加以下几个字段

|products|类型|备注|
|---|--|
|id|
|title|
|description|
|promotion_actived|boolean| 优惠进行中？（新）|
|promotion_title|string| 优惠活动（新）|
|promotion_started_at|datetime|优惠开始时间（新）|
|promotion_ended_at|datetime|优惠截至时间（新）|
|promotion_discount|decimal|优惠额度（元）（新）|
  
在 orders 表中添加以下几个字段

|orders|备注||
|---|--|
|id|
|sum_price|
|shipment_price|
|cellphone|
|address|
|...|
|description|
|promotion_actived| 优惠进行中？（新）|
|promotion_title|优惠活动（新）|
|promotion_started_at|优惠开始时间（新）|
|promotion_ended_at|优惠截至时间（新）|
|promotion_discount|优惠额度（元）（新）|

在代码逻辑里判断是否满足条件，如果满足条件，就对用户折扣。

但是这样做有个问题，以后如果优惠规则有变化，比如根据用户等级打折时，系统的代码逻辑就会异常复杂。接手这个项目的人无法一目了然的理清 promotion 的逻辑。

这样的代码不符合 Vincent 的审美，我只好重做了。

## 设计思路 二

无论是针对订单、还是针对单品的 Promotion，都有一些共同属性。尝试把他们抽象出来，变成成为一个独立的 Model。

|promotions|备注||
|---|--|
|promotion_actived| 优惠进行中？（新）|
|promotion_title|优惠活动（新）|
|promotion_started_at|优惠开始时间（新）|
|promotion_ended_at|优惠截至时间（新）|
|promotion_discount|优惠额度（元）（新）|

然后通过 STI 的方式，创建不同的子类 GoodsPromotion、UserPromotion、OrderPromotion。

不同的 model 与不同的优惠建立关系

Goods <=> GoodsPromotion  
Order <=> OrderPromotion  
User <=> UserPromotion  

这样处理后，所有的促销逻辑一目了然，结构富有弹性。系统以后再添加促销活动时也比较容易。

<span class="image-800">![Dropbox]({{ site.url }}/media/files/2014/mar/08-promotion.png)</span>


## 总结

我已经把电商的小把戏和盘托出了。

1. 不要为了打折而购买不需要的东西。
2. 不要担心错过打折，电商的打折时刻都存在，只是换了一个噱头而已。
3. 购物真他妈浪费时间。
