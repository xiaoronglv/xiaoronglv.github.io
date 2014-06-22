---
title: 一张图理解 Strong Parameter
layout: post
guid: yk6W1Jt5zJzz
date: 2014-06-22 15:00:00
tags:
   - Rail
---

在 Rails3 中创建或更新 Active Record 对象时，会有 Mass Assignment 安全问题。所以 Model 中需要列一个白名单，声明哪些属性可以被 parameter 的数据更新。

## Rails 3

    # kings_controller.rb
    def create
      #{ name: ‘David’, sex:male, age: 31}
      @king = King.new(params[:king])
      if @king.save
        redirect_to @king
      else
        render 'new'
      end
    end


    # king.rb
    class King
      attr_accessible :name
    end


## Rails 4

Rails 4 引入了 Strong Parameters 的机制，Model 不再负责白名单的维护，把过滤非法属性的职责推给了 Controller。

    # kings_controller.rb
    def create
      # new parameter { name: ‘David' }
      @king = King.new(king_params)
      if @king.save
        redirect_to @king
      else
        render 'new'
      end
    end

    private

    def  king_params
      # old parameter  { name: ‘David’, sex:male, age: 31}
      # new parameter { name: ‘David' }
      params[:king].permit(:name)
    end


    # king.rb
    class King

    end 

## 什么是 Strong Parameters?

![Strong Parameters]({{ site.url }}/media/files/2014/Jun/22-strong-parameters.png)

说白了 Strong Parameter 其是就是一层白名单过滤。

1. View 层穿过来的数据会转化为一个 ActionController::Parameters 对象

2. 过滤老的 ActionController::Parameters 对象，生成一个新的 ActionController::Parameters 对象。

    * 只保留白名单属性
    * 实例变量 @permitted  赋为 true

3. 把过滤后的 ActionController::Parameters 对象传给 model，创建或更新对应的的 ActiveRecord 对象。

## 可以硬传给 model，霸王硬上弓吗？

未经 Strong Parameter 过滤的 ActionController::Parameters 对象的 @permitted 为 false（过滤后为 true）。如果硬传给 Model，会报错 ActiveModel::ForbiddenAttributesError 。 


