---
title: Ruby Dynamic Method
layout: post
guid: Oyr6mMplWU09
tags:
   - Ruby 
---

此文为 *Ruby 元编程* 第二章的读书笔记。

## 静态 Vs 动态

静态语言：C/java 在代码运行前编译，编译时必须保证所有的对象调用的方法都是提前定义好的。

动态语言：压根不会检查方法是否被定义，只有真正执行时，才会按照「向右一步，向上走」的顺序查找方法。

## 动态方法

1. 动态派发

    把 a、b、c、d、e 等重复的方法代理到 component 方法上。


        def mouse
          component :mouse
        end
        
        def cpu
          component :cpu
        end
        
        def keyboard
          component :keyboard
        end
        
        def component(name)
          info = @device.send "get_#{name}_info"
          return info
        end

     

2. 动态创建

        class computer
        
          def self.define_method(name)
            def_method(name) do
              ...
            end
          end
        
          define_method(mouse)
          define_method(keyboard)
          define_method(cpu)
        end


## 幽灵方法（Ghost Method）

一个对象「向右走一步，向上走」遍历所有依然找不到方法时，会调用 Kernal#method_missing。我们可以在这个方法里做一些手脚，作为补救方案。

藏在 method_missing 中的方法即为幽灵方法。

## Dynamic Method Vs Ghost Method

|区别|Dynamic Method|GHost Method|
|---|--|
|是否真实存在|true|false|
|respond_to?|true|false|

