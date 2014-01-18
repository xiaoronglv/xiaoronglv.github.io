---
title: 如何捏对象
layout: post
guid: kBsF9EjhHaE4
tags:
  - Rails
---

在电商系统中，经常要把一组对象加减乘除，生成报表。怎么处理这种需求呢？

方案1：按照统计要求创建一个model，将计算好的数据保存在里面。

方案2：使用group方法，把一组对象捏合为一个对象。

今天我说一下使用第二种方案的感受。

## Active Record Query Interface: Group

这是 Group 方法的官方文档[1]。

To apply a GROUP BY clause to the SQL fired by the finder, you can specify the group method on the find.
For example, if you want to find a collection of the dates orders were created on:

    Order.select("date(created_at) as ordered_date, 
                  sum(price) as total_price").group("date(created_at)”)

** And this will give you a single Order object for each date where there are orders in the database. ** 
The SQL that would be executed would be something like this:

    SELECT date(created_at) as ordered_date, sum(price) as total_price
    FROM orders
    GROUP BY date(created_at) 

使用 Group 可以做些什么呢？为了让你更好的呃理解，我讲一下我的应用case。


## 原始数据

这个表创建的意义：记录每个商品每天的销量，销售总额

* the_date: 日期
* goods_id: 商品id
* quantity: 该商品在当日的销售数量
* amount: 该商品在当日的销售额度

<span class="image-800">![how to draw a horse]({{ site.url }}/media/files/2014/jan/17-raw-data.png)</span>

## 应用1：如何统计某个时间段内的热销商品？

    # controller
    def best_selling
      @stat = GoodsStat.best_selling(params[:start_date], params[:end_date])
    end

    # model
    def self.best_selling(start_date, end_date)
      start_date = start_date.to_date
      end_date  = end_date.to_date

      where("the_date >= ? and the_date <= ?", start_date, end_date).
        group("goods_id").
        select("goods_id,
              sum(quantity) as quantity,
              sum(amount) as amount).order("quantity DESC").all
    end

根据 goods_id 把一组数据「捏为」一个对象，这个对象的属性：

* quantity：某商品在时间段内的销售总量
* amount：某商品在时间段内的销售总额


## 应用2：统计每天的销售总额

    #controller
    def daily
      @stat = GoodsStat.best_selling(params[:start_date], params[:end_date])
    end

    #model
    def self.best_selling(start_date, end_date)
      start_date = start_date.to_date
      end_date   = end_date.to_date
    
      where("the_date >= ? and the_date <= ?", start_date, end_date).
        group("the_date").
        select("goods_id,
                sum(quantity) as quantity,
                sum(amount) as amount).order("quantity DESC").all
    end 

根据 the_date 把某一天的所有对象「捏为」一个对象，这个对象的属性：

* quantity：某一天销售的商品总数量
* amount：某一天销售的商品总额度


## 参考

1. [Rails Guide: Group, Active Record Query Interface](http://guides.rubyonrails.org/active_record_querying.html#group)


