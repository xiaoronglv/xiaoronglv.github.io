---
title: 异步任务的依赖
layout: post
guid: JKMK08wUtzp21
tags:
   - Rails
---
<span class="image-800">![Dropbox]({{ site.url }}/media/files/2014/mar/02-dog-fast-car-goggles.jpg)</span>

这周遇到了一个非常复杂的bug，花了我两个下午的时间。

薄荷商店是我们 App 的一个模块，用户可以在薄荷商店购买健康食品。我们的各个产品经理会为用户精挑细选一些进口商品。

这周我的工作是通过用户的订单数据（orders表），产生一个报表，反映每个产品经理的销量和利润（发奖金喽）。

## 场景

1. 为了实现这个功能，我新建了一个表 crm_orders 做冗余数据。所有的统计工作都是在这个表上进行，以免干扰正常的销售业务。

2. 当用户支付订单后，系统通过异步的方式加工这份订单，并将统计数据更新至 crm_orders。

异步使用的是多线程的 sidekiq。

    class GoodsOrder  
      def change_to_paid!(options = {})  
        GoodsOrder.delay.sync_to_crm(self.id)  
      end  
    end  


## 问题

代码上线半天后，问题接踵而至。好多条记录 paid_at 字段的值是 NULL？

table：crm_orders

| id    |  paid_at   | manager_id | goods_id | order_id | quantity  |
|-------|------------|------------|----------|----------|-----------|
| 14116 | 2014-02-28 |         21 |       98 |   321046 |        1  |
| 14115 | 2014-02-28 |         21 |       95 |   321046 |        1  |
| 14114 | NULL       |         30 |       60 |   321046 |        1  |
| 14113 | 2014-02-28 |         30 |       60 |   321028 |        2  |
| 14112 | NULL       |         21 |       63 |   321020 |        1  |
| 14111 | NULL       |         28 |       71 |   321020 |        1  |



## 第一反应：method 有没有写错？

虽然已经写了详细的 test case，难免漏网之鱼。拿错误数据测试一下，以排除这方面的疑虑。

    > RAILS_ENV=production rails c
    > GoodsOrder.sync_to_crm(321020)

table：crm_orders

| id    |  paid_at   | manager_id | goods_id | order_id | quantity  |
|-------|------------|------------|----------|----------|-----------|
| 14112 | 2014-02-28 |         21 |       63 |   321020 |        1  |
| 14111 | 2014-02-28 |         28 |       71 |   321020 |        1  |

订单被处理后，统计数据正确无误，method 没有问题。到底哪里出问题了呢？

## 第二反应：异步任务有没有被正确执行？

sidekiq 的 log 中有大量的出错信息，原来是某台 server 发布时，sidekiq 进程重启失败。我喜出望外，以为找到问题的源头。

但是异步任务失败的话，应该没有数据，而不是产生错误的数据！看来这并不是 bug 真正的源头。

果不其然，重启 sidekiq 后，log 中不再出现错误日志，但是数据库中的错误数据依然源源不断的产生。

## 问题的源头

一个下午已经过去了，我还是没找到 bug 的源头，心情也愈发烦躁。

Vincent 的一句话点醒了我：你的异步任务是不是执行的太快了？

    class Payment
      def change_to_paid!
        self.class.transaction do
          self.state = :paid
          self.paid_at = Time.now
          save!
          # 支付后更新订单的支付日期 paid_at
          order.change_to_paid! # 问题就出在这一步
        end
      end
    end
    
    class GoodsOrder
      def change_to_paid!
        paid_at = Time.now
        GoodsOrder.delay.sync_to_crm(self.id)      
      end
    end

我的异步任务确实执行的太快了。

`GoodsOrder#change_to_paid!`  调用时被包含在一个 transaction 中。

真实的执行顺序有可能是这样的：

1. `order.change_to_paid!` 中的异步任务开始执行，此时 paid_at 还未产生，所以部分统计数据 `paid_at` 为空。

2. transaction commit


## 问题修复

后来我将代码修改为这样样子，问题就解决了。

    class GoodsOrder
      def change_to_paid!(options = {})
        GoodsOrder.delay_for(5.minutes).sync_to_crm(self.id)
      end
    end


# 总结

这个 bug 让我使用异步时更加谨慎了，我会问自己三个问题：

1. 该异步身处一个事务中吗？

2. 该异步依赖其他异步任务吗？

3. 执行的先后顺序都搞清楚了吗？
