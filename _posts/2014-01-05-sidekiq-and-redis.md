---
title: 我对 Sidekiq 的理解
layout: post
guid: 0TMt3GCAXS5P
tags:
  - Ruby
  - Rails
---

<span class="image-800">![network]({{ site.url }}/media/files/2014/jan/1-sidekiq.png)</span>

## Consumer：sidekiq 进程

1. Sidekiq会加载Rails的代码，所以可以使用 Rails app 的各种类、各种方法等等
2. 更新Rails代码后，Sidekiq 进程需要重新启动，以便reload代码。
3. Sidekiq 是多线程，默认25个


## Publisher：Rails

1. Sidekiq通过Redis Gem（一个Redis client）和Redis通讯。

2. Rails通过Sidekiq，向Redis存储数据


## Redis

Redis是内存数据库
Redis默认有16个库
