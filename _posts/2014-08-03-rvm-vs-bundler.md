---
title: Rvm 与 Bundler 的爱恨情仇
layout: post
guid: 10lyyQQfwovB
date: 2014-08-03 22:23:00
tags:
   - Ruby
---


自从2年前 Sam 帮我搭建了 Ruby 环境，除了升级就再没折腾过。懒，不爱折腾，太花时间，投入产出不成正比。

但是在开发的过程中，越来越多 Rvm & Bundler 的问题摆在了我面前，每次都要请教别人，十分的影响效率。

于是花了一个上午的时间彻底把 Rvm 和 Bundler 的关系梳理了一下。

## Rvm

Rvm 用于管理 Ruby 的版本和该版本的 Gem。

![Rvm]({{ site.url }}/media/files/2014/Aug/3-rvm)

一开始我很疑惑，为什么同一个 Gem，同一个版本号，要保存四份？

据大牛回答，有些 Gem 需要编译， 所以依赖 Ruby 版本。比如在 Ruby 1.9.3 的环境下编译的 `libv8 (3.11.8.17）`不一定能在 Ruby 2.0.0 中使用。

所以即使相同版本号的 Gem，也要在各个 Ruby 各自编译，各自保存。[1]


## Rvm Gemsets

事实上 Rvm 在管理 Gem 的方式上要比图一还要复杂 一些，它的内部还有一套 Gemsets 的机制。

我把 Gemset 理解为一个环境，这个环境中罗列了 Gem 的版本号。

![Gemset]({{ site.url }}/media/files/2014/Aug/3-Gemset.png)

1. Ruby 版本
2. Gemset 环境
3. Gem 列表


Gemset 大体可分为三类：

1. @global
    
    比如：ruby-1.9.3-p448@global

    安装在 @global 中的 Gem 会被其他 Gemset 继承。 假如我在 `ruby-1.9.3-p448@global` 中安装了 `bundler-1.1.5`，那么 `ruby-1.9.3-p448@codecampo` 也可以使用 `bundler-1.1.5`。

2. @default

    比如：`ruby-1.9.3-p448`
    
    若不指定 Gemset，Gem 默认都安装在默认环境下。
    
3. @custom

    自定义的 Gemset，比如 
        
    * ruby-1.9.3-p448@ruby_china
    * ruby-1.9.3-p448@codecampo
    * ruby-1.9.3-p448@19wu


你可以创建一个 `ruby-1.9.3-p448@ruby_china` 的环境用来跑 Ruby China，你也可以创建 `ruby-1.9.3-p448@codecampo` 跑 codecampo。


## What's the fucking difference between Rvm Gemset and Bundler Gemfile?

很早之前我有类似的疑问：我的每个项目都使用 Bundler 生成 Gemfile、Gemfile.lock 管理版本号和依赖关系。为毛还需要 Rvm Gemset？

我们现在的确不需要！

但，爱过。

Rvm 诞生于 Bundler 之前，并通过 Gemset 这种的式来管理 Gem。

随后 Bundler  才兴起，大家几乎都一窝蜂似的用它管理项目中涉及 Gem 的版本号、依赖关系。从而导致了 Rvm 的 Gemset 处于十分尴尬的地位。




[1] 貌似有特例：若同一个 Gem 在多个 Ruby 版本中都能使用的话，Rvm 会直接软链过去。

