---
title: 为什么要使用 bundler exec
layout: post
guid: DHGqt4tMOFAZ
date: 2014-09-14 20:23:00
tags:
   - Ruby
---

通常开发环境下，我们使用 `rails c` 启动 console；使用 `rails s` 来启动 WebRick。这其实是一种碰运气的行为。

rails c 在加载 Gem 时并没有考虑当前项目所以来的环境，只是简单的加载。

1. 通过 Gemfile 文件查找当前项目依赖的所有 Gem

        source "http://boohee:boohee0690@gems.boohee.cn"
        source 'http://ruby.taobao.org'

        gem 'rails', '3.2.14'
        gem 'mysql2'
        ...


2. 加载当前 Gemset 中已经安装的 Gem


因为忽略了 gem 的版本号，在某些项目中，这样搞会出错。我们可以在命令前加上 `bundle exec rails c`，又可以正常的启动 console 了。

## bundle exce 到底有什么用途？

> 官方给出的解释 [bundle-exec](http://bundler.io/v1.3/man/bundle-exec.1.html)

> Execute a command in the context of the bundle

> In some cases, running executables without bundle exec may work, if the executable happens to be installed in your system and does not pull in any gems that conflict with your bundle. However, this is unreliable and is the source of considerable pain. Even if it looks like it works, it may not work in the future or on another machine.


也就是说，bundle exec 会在当前项目依赖的上下文环境中执行命令。

1. 相同的 gem
2. 相同的依赖关系
3. 相同的版本号
