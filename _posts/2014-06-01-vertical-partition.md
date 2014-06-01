---
title: 垂直分割
layout: post
guid: czWtEeV1dCwI
date: 2014-06-01 13:00:00
tags:
   - Ruby
---

![Vertical Partition]({{ site.url }}/media/files/2014/Jun/1-vertical-partition.gif)

## Bad design

商品表 (products) 有以下几个属性

    CREATE TABLE `products` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `sku` varchar(255) DEFAULT NULL,
      `title` varchar(255) NOT NULL,
      `state` varchar(255) NOT NULL,
      `price` decimal(10,2) NOT NULL,
      `weight` decimal(10,4) NOT NULL,
      `default_photo` varchar(255) DEFAULT NULL,
      `created_at` datetime NOT NULL,
      `updated_at` datetime NOT NULL,
      `type` varchar(100) DEFAULT NULL,
      `detail` text DEFAULT NULL,
      PRIMARY KEY (`id`),
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

「detail」属性保存商品的图文页面，内容巨大，只在详情页才会用到。

其他场景中用不到商品的 detail 属性。但每次调用 product = Product.find(id), Rails 都会将整条记录（包括 detail）对象化，十分浪费内存。

## Good design

将 detail 属性从 products 拆出来，放到一张单独的表（product_details）中， 只在需要的时候才加载 detail 信息，这是垂直分割[1]。

class

    # class
    class Product
      has_one :product_detail
    end

    class ProductDetail
      belongs_to :product
    end

tables

    # two tables
    CREATE TABLE `products` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `sku` varchar(255) DEFAULT NULL,
      `title` varchar(255) NOT NULL,
      `state` varchar(255) NOT NULL,
      `price` decimal(10,2) NOT NULL,
      `weight` decimal(10,4) NOT NULL,
      `default_photo` varchar(255) DEFAULT NULL,
      `created_at` datetime NOT NULL,
      `updated_at` datetime NOT NULL,
      `type` varchar(100) DEFAULT NULL,
      PRIMARY KEY (`id`),
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

    CREATE TABLE `product_details` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `goods_id` int(11) DEFAULT NULL,
      `detail` text DEFAULT NULL,
      `created_at` datetime NOT NULL,
      `updated_at` datetime NOT NULL,
      PRIMARY KEY (`id`),
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

controller

    # products_controller.rb
    def show
      product = Product.includes(:product_detail).find(params[:id])
    end


Vertical partitioning involves creating tables with fewer columns and using additional tables to store the remaining columns. Normalization also involves this splitting of columns across tables, but vertical partitioning goes beyond that and partitions columns even when already normalized. Different physical storage might be used to realize vertical partitioning as well; storing infrequently used or very wide columns on a different device, for example, is a method of vertical partitioning. Done explicitly or implicitly, this type of partitioning is called "row splitting" (the row is split by its columns). A common form of vertical partitioning is to split dynamic data (slow to find) from static data (fast to find) in a table where the dynamic data is not used as often as the static. Creating a view across the two newly created tables restores the original table with a performance penalty, however performance will increase when accessing the static data e.g. for statistical analysis.

## Reference

1. [Wikipedia: Partition](http://en.wikipedia.org/wiki/Partition_%28database%29)

2. [what's the difference between horizontal and vertical partitions?](http://stackoverflow.com/questions/18302773/what-are-horizontal-and-vertical-partitions-in-database-and-what-is-the-differen)