---
title: Ruby tips 不要使用多个 Gem 源
layout: post
guid: j5t8j8kwpncs
date: 2014-11-29 09:31:31
tags:
   - 
---

![What is ruby gems]({{ site.url }}/media/files/2014/Nov/2014-11-29-what-is-a-ruby-gem.jpg)


# Gem

因为 https://rubygems.org 访问速度不佳，所以国内的同学都默认使用淘宝的镜像做备用的 Gem 源，以便加快安装 Gem 的速度。

为了一劳永逸，我直接把淘宝放到电脑的 Gem source 中，希望以后安装 Gem 时，默认走淘宝，速度更快。

```text
> gem sources 
*** CURRENT SOURCES ***

http://ruby.taobao.org
https://rubygems.org

```

事实上这种 multi-sources 的方式一点都不快，反而变慢了！我把整个安装过程打印了出来发现：如果你有多个 Gem 源，它会挨个访问一遍，安装 Gem 的最新版本。 

```text
/Users/xiaoronglv [8:39]
> gem install sidekiq --verbose
GET http://ruby.taobao.org/latest_specs.4.8.gz
302 Moved Temporarily
GET http://rubygems-china.oss.aliyuncs.com/latest_specs.4.8.gz
200 OK
GET https://api.rubygems.org/latest_specs.4.8.gz
302 Moved Temporarily
GET http://ruby.taobao.org/quick/Marshal.4.8/sidekiq-3.3.0.gemspec.rz
302 Moved Temporarily
GET http://rubygems-china.oss.aliyuncs.com/quick/Marshal.4.8/sidekiq-3.3.0.gemspec.rz
200 OK
HEAD http://ruby.taobao.org/api/v1/dependencies
200 OK
HEAD https://api.rubygems.org/api/v1/dependencies
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=sidekiq
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=sidekiq
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=redis
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=redis
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=redis-namespace
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=redis-namespace
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=connection_pool
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=connection_pool
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=celluloid
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=celluloid
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=json
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=json
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=timers
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=timers
200 OK
GET http://ruby.taobao.org/api/v1/dependencies?gems=hitimes
200 OK
GET https://api.rubygems.org/api/v1/dependencies?gems=hitimes
200 OK
GET https://api.rubygems.org/quick/Marshal.4.8/sidekiq-3.3.0.gemspec.rz
302 Moved Temporarily
GET https://au-m.rubygems.org/quick/Marshal.4.8/sidekiq-3.3.0.gemspec.rz
200 OK
Downloading gem sidekiq-3.3.0.gem
GET https://api.rubygems.org/gems/sidekiq-3.3.0.gem
302 Moved Temporarily
GET https://au-m.rubygems.org/gems/sidekiq-3.3.0.gem
Fetching: sidekiq-3.3.0.gem (100%)
200 OK
1 gem installed

```


# Bundler

同样，有些同学会在 Gemfile 中指定多个 Gem 源。

```ruby
source 'https://rubygems.org'
source 'http://ruby.taobao.org'

gem 'sidekiq'  
gem 'redis-object'
```

[Bundler 文档](http://bundler.io/v1.3/man/gemfile.5.html#SOURCE-PRIORITY) 也有详细的说明。

When attempting to locate a gem to satisfy a gem requirement, bundler uses the following priority order:

1. The source explicitly attached to the gem (using :path or :git)

2. For implicit gems (dependencies of explicit gems), any git or path repository otherwise declared. This results in bundler prioritizing the ActiveSupport gem from the Rails git repository over ones from rubygems.org

3. The sources specified via source, **searching each source** in your Gemfile from last added to first added.


Bundler 会搜索所有的源，安装速度也不快。


# Conclusion

如果你只是一个 Gem 的使用者，对 Gem 的实时性没有很高的要求（< 10min），就老老实实的用淘宝的 Gem 源吧，安装速度快很多。

如果你是一个 Gem 的开发者，提交 rubygems 后，淘宝十几分钟才会出现。
