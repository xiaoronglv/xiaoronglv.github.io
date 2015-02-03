---
title: 部署 puma 后 Gemfile 没有刷新
layout: post
guid: tf9kfb1lydn6
date: 2015-02-01 19:19:42
tags:
   -
---

![](http://www.atmjournal.org/images/atm/ATM-PubMed-Icon-120X30.png )

前段时间在使用 puma 时遇到一个大坑，相信很多人以后也会碰到。

我的某个项目的生产环境这个配置的：

* puma mode：cluster
* worker: 4
* thread: 8,32
* 部署工具: capistrano

在某次发布中，代码引入了一个新的 Gem slack-notifier。使用 capistrano 部署后，puma 的代码虽然更新了，但是 Gem 还是用的老的，而且新引入的 Gem 没有找到。

log 中一直报错：`uninitialized constant Slack::Notifier`

我当时很排查了很久，特别纳闷：代码都更新了，并且成功重启了，为什么 Gemfile 没有刷新呢？

Puma 有两种重启方式 Phased restart 和 Normal restart，难道是我重启的姿势不对？

但是分别试了两种方式重启，Gemfile 还是没有刷新。


## 解决方法

Google 后发现，吐槽的不止我一个人。


* [Gemfile not refreshed between deploys](https://github.com/puma/puma/issues/300)

* [Puma phased-restart fails when Gemfile is changed](http://stackoverflow.com/questions/22811784/puma-phased-restart-fails-when-gemfile-is-changed)



在 stackoverflow 上找到一个答案：假如使用 capistrano 部署项目，在 deploy.rb 中增加以下两个设置就可以解决这个问题。

    set :puma_preload_app, false
    set :puma_prune_bundler, true

但，这两个参数是干嘛的？


[prune_bundler](https://github.com/puma/puma/blob/master/examples/config.rb#L132) allow workers to reload bundler context when master process is issued a USR1 signal. This allows proper reloading of gems while the master is preserved across a phased-restart. (incompatible with preload_app) (off by default)


按照官方文档的意思，如果我们想在部署时刷新 Gemfile，必须特意指明 phased restart 的方式，并且禁用 preload_app!


看到这，我很想吐槽：项目修改 Gemfile 是很平常的事情。Puma 默认居然不做处理，还需要特别配置，坑爹！
