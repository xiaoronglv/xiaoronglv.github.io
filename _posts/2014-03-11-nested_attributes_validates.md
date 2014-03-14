---
title: accepts_nested_attributes_for 的验证问题
layout: post
guid: eRgwefyeEhVo
tags:
   - Rails
---

出于性能的考虑，对商品表做了垂直分割，用两张表来保存商品的信息。

* 商品的概览（products）

    ||
    |---|
    |id|
    |title|
    |price|

* 商品的图文介绍（products_details）

    ||
    |---|
    |id|
    |product_id|
    |description|


为了在一个表单中保存编辑两个对象，我使用了 Rails 的 nested attributes。

## Model

    class Product < ActiveRecord::Base
      has_one :product_detail, dependent: :destroy
      attr_accessible :title, :price, product_detail_attributes
      accepts_nested_attributes_for :product_detail
    end
    
    class ProductDetail < ActiveRecord::Base
      belong_to :product
      
      #此处是个坑
      #validates :product_id, presence: true
    end

## View

    <%= simple_form_for [:admin, product] do |f| %>
      <%= f.input :title, label: "产品名"%>
      <%= f.input :price, label: "市场价" %>
      <%= f.simple_fields_for :product_detail do |detail| %>
        <%= detail.input :markdown_description, label: "产品描述", as: :text %>
      <% end -%>
    <% end -%>

## Controller

    def  new
      @product = Product.new
      
      # 如果是 has one，则使用 build_** 初始化
      @product_detail = @product.build_product_detail
      
      # 如果是 has many，则使用 *s.build 初始化
      # @promotion = @product.promotions.build
    end

    def create
      @product = Product.new(params[:product])

      if @product.save
        redirect_to @product
      else
        render ‘edit’
      end
    end


## 事务

当 @product.save 时，二个对象的创建被包裹在一个事务中，同时创建。

part1. 创建 product

part2. 创建 product_detail


## log

    (0.2ms)  BEGIN
    SQL (0.3ms)  INSERT INTO `products` ...
    SQL (0.4ms)  INSERT INTO `product_details` ...
    (206.7ms)  COMMIT


## 一个和验证相关的 bug

一开始的时候 product 和 product_detail 总是无法同时创建，log 打印出错误信息 'product_detail 的 product_id 不能为空'。

花了 2h 排插各种错误，无果。

    class ProductDetail < ActiveRecord::Base
      belong_to :product
      
      # 注释掉后就可以保存 product_detail了
      validates :product_id, presence: true
    end

后来发现是一个狗血的验证导致的。

为什么这个验证会导致创建失败呢？
product_detail 有 product_id 这个属性啊？

后来 Vincent 指点：

1. product 和 product_detail 创建时，会整体上检查对象是否有效（valid?）

2. 此时 product 还没有创建， product_detail 无法获得 prodcut_id

3. 所以 product_detail 验证失败。


如果两个对象的创建包裹在一个事务中时，第二个对象的某些属性可能暂时为空，导致验证失败？




 
