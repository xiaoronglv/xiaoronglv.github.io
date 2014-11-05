---
title: 使用 Factory Girl 作默假数据生成器
layout: post
guid: r4ezz6i8djqt
date: 2014-11-05 22:17:23
tags:
   - Ruby
---

使用 [factory\_girl\_rails](https://github.com/thoughtbot/factory_girl_rails) 可以把 factory girl 集成到 rails 中，实现一些很方便的功能。


1. factory girl 作默认的假数据生成器，替代 fixture.
2. 自动加载多个目录下的所有的 factries，比如 test/factories，spec/factories.
3. 自定义配置 namespace.
4. 等等.

本周在配置新项目的时候，发现了一个很奇葩的 bug。我已经在 test group 中添加了 factory\_girl\_rails，但是用脚手架创建新的 model 时 `rails g scaffold article title:string content:text`，rails 依然生成了 fixture，Why？

```Ruby
# Gemfile
group :development do
  gem 'mongoid_colored_logger'
  gem 'awesome_print'
end

group :test do
  gem 'turn'
  gem 'factory_girl_rails'
  gem 'database_cleaner'
  gem 'mocha', require: false
end
```

![replace fixture]({{ site.url }}/media/files/2014/Nov/2014-11-05-bug.jpg)

## 解决方法

后来在读官方文档中，才发现这么一句话。

> If you use factory_girl for fixture replacement, ensure that factory\_girl\_rails is available in the development group. If it's not, Rails will generate standard .yml files instead of factory files.

如果要使用 factory_girl 替代 fixture， development 环境也必须加载它。调整一下 gem 的分组就把这个问题解决了。


```Ruby
# Gemfile
group :development do
  gem 'mongoid_colored_logger'
  gem 'awesome_print'
end

group :test do
  gem 'turn'
  gem 'database_cleaner'
  gem 'mocha', require: false
end

group :development, :test do
  # development 和 test 环境都加载它
  gem 'factory_girl_rails'
end

```

说实话，这个篇文章没啥技术含量，主要是担心不少同学会踩到这个坑，所以写下来，希望有所帮助。
