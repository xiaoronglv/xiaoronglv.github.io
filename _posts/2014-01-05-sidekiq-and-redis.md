---
title: 我对 Sidekiq 的理解
layout: post
guid: 0TMt3GCAXS5P
tags:
  - Ruby
  - Rails
---

<span class="image-800">![network]({{ site.url }}/media/files/2014/jan/1-sidekiq.jpeg)</span>

## Consumer：sidekiq 进程

1. 会加载 rails 的代码，所以可以使用 Rails app 的各种类、等等
2. 更新 rails 代码后，需要sidekiq 进程需要重新启动
3. sidekiq 是多线程，默认25个


## Publisher：Rails

1. sidekiq通过redis gem（一个redis client）和redis通讯。

2. Rails通过sidekiq，向redis存储数据


## Redis

redis 用来保存数据
