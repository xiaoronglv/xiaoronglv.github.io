---
title: Sidekiq 和 Unicorn 搭配时的一些奇妙事
layout: post
guid: fidu8t2bs9ck
date: 2014-12-14 22:47:28
tags:
   -
---

Sidekiq 是多线程的异步处理程序，应用广泛。Unicorn 是多进程的 Server 容器。当他们搭配的时候，需要注意一些奇妙的事情。


## sidekiq 与 unicorn 搭配时的问题

一般每个 Unicorn worker 的数据库连接池 pool = 5。

```
default: &default
  adapter: mysql2
  encoding: utf8
  reconnect: false
  pool: 5
  timeout: 5000
```

Sidekiq 进程默认会启动 25个线程。假如 Sidekiq 使用 database.yml 规定的连接池数量 poll=5, 有20个线程只能在哪傻等，什么事情也干不了。


## 解决方法1：把sidekiq 的线程数调为数据库 pool 的值。

在 sidekiq.yml 中配置，降低线程数

```
---
:concurrency: 5
:pidfile: tmp/pids/sidekiq.pid
qa:
  :concurrency: 5
production:
  :concurrency: 5
:queues:
  - default
```


## 解决方法2： 启动 sidekiq 时，把 ActiveRecord 的数据库 pool 调高


As of Rails 3.2, ActiveRecord's initialization code will prefer a DATABASE_URL over the database.yml file. (See Issue #503 for a more complete explanation.) You can set a custom connection pool size for the Sidekiq server process via:


```
Sidekiq.configure_server do |config|
  config.redis = { url: 'redis://redis.example.com:7372/12', namespace: 'mynamespace' }

  database_url = ENV['DATABASE_URL']
  if database_url
    ENV['DATABASE_URL'] = "#{database_url}?pool=25"
    ActiveRecord::Base.establish_connection
  end
end
```
