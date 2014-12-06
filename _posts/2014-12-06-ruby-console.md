---
title: irb, bundle exec irb, bundle console, rails console 的区别
layout: post
guid: 1h9g1135iuli
date: 2014-12-06 11:53:13
tags:
   - 
---

我们可以使用多种命令启动 Ruby console：

1. irb
2. bundle exec irb
3. bundle console
4. bundle exec rails console

他们之间有什么关系呢？


## irb

使用 irb 时，它不关心你的 Gemfile 和 Gemfile.lock，它就是一个裸的 Ruby console，只加载 Ruby 的核心库。

1. 你如果要使用 RestClient，手工 require 'rest_client'
2. 你如果要使用 MySQL ，手工 require 'mysql2'
3. 如果你要是用 Rack，手工 require 'rack'

并且当你在 irb 中 require 'rack' 时，它默认使用最高版本的 rack。

    /Users/xiaoronglv [13:05]
    > gem list rack

    *** LOCAL GEMS ***

    rack (1.5.2, 1.5.1)
    rack-test (0.6.2)

    /Users/xiaoronglv [13:05]
    > irb
    2.1.5 :001 > require 'rack'
     => true 
    2.1.5 :002 > Gem.loaded_specs["rack"].version.to_s
     => "1.5.2" 
    


总结

1. irb 不会考虑 Gemfile 和 Gemfile.lock；
2. require 默认使用最新的版本的 Gem。

> 备注：
>  
> Ruby 1.9.* 以上的版本默认集成了 [rubygem](https://github.com/rubygems/rubygems).
>   
> require 最新版本的 gem 不是 Ruby 本来的的行为，是 rubygems 通过 monkey patch 重写了 kernal#require 方法。[查看源码](https://github.com/rubygems/rubygems/blob/0f01103a4607077a6a63df2e3c06cf06e7203b01/lib/rubygems/core_ext/kernel_require.rb)

## bundle exec irb
    
    # 我有两个版本的 rack
    > gem list rack

    *** LOCAL GEMS ***

    rack (1.5.2, 1.5.1)
    rack-test (0.6.2)


    # /tmp/christ/Gemfile
    source 'http://ruby.taobao.org'
    gem 'rack'

    # 使用老版本的 rack
    # /tmp/christ/Gemfile.lock
    GEM
      remote: http://ruby.taobao.org/
      specs:
        rack (1.5.1)

    PLATFORMS
      ruby

    DEPENDENCIES
      rack


使用 `bundle exec irb` 时，bundler 会把当前文件夹下 Gemfile  和 Gemfile.lock 指定版本的 gems 列表添加到 $LOAD_PATH 中。

    /tmp/christ [12:34]
    > bundle exec irb
    2.1.5 :001 > puts $LOAD_PATH
    /Users/xiaoronglv/.rvm/gems/ruby-2.1.5/gems/rack-1.5.1/lib
    ...


当我 `require 'rack'` 时，会使用指定版本的 rack。


    2.1.5 :001 > require 'rack'
     => true 
    2.1.5 :002 > Gem.loaded_specs["rack"].version.to_s
     => "1.5.1" 


总结：

1. bundle exec irb 会把 Gemfile  和  Gemfile.lock，指定的版本的 gems 添加到 $LOAD_PATH。(Bundler.setup)

2. require gem 时，使用 Gemfile.lock 的版本号。


## bundle console

bundle console 比 bundle exec irb 多做了一些工作。

1. 把  Gemfle 和 Gemfile.lock 指定版本呢的 gems 添加到了 $LOAD_PATH。(Bundler.setup)


        > bundle console   
        2.1.5 :001 > puts $LOAD_PATH
        /Users/xiaoronglv/.rvm/gems/ruby-2.1.5/gems/rack-1.5.1/lib
        ...


2. require 所有的 gems。(Bundler.require)


        2.1.5 :002 > puts $LOADED_FEATURES
        ....
        /Users/xiaoronglv/.rvm/gems/ruby-2.1.5/gems/rack-1.5.1/lib/rack.rb
        ... 


它不仅把 Gemfile 和 Gemfile.lock 中的 gem 添加到了 $LOAD_PATH，还自动帮我们加载了。


## bundle exec rails console


bundle exec rails console 做的工作更多

1. require Gemfile 的 gem，并且严格遵守其版本号。
2. require 整个 rails 环境
3. setup autoloading
4. initialize app


# 总结

为了便于比较，我做了一个不太严谨的表格。

|  命令  |  gems 添加到 $LOAD_PATH?(Bundler.setup) |  gems required? (Bundler.require)  |   Rails required?  |
| ------------  | ------------- | ------------ | ------------ |
| irb | 否  | 否 | 否 |
| bundle exec irb | 是  | 否 | 否 |
| bundle console | 是  | 是 | 否 |
| bundle exec rails console | 是  | 是 | 是 |


> 备注：  
>
> gems： Gemfile 和 Gemfile.lock 指定版本号的所有 gems。


本文大部分内容翻译自这篇文章，我根据自己的理解做了一些调整。

[What's the Difference Between irb, bundle exec irb, bundle console, and rails console?](http://www.justinweiss.com/blog/2014/11/17/what-are-the-differences-between-irb)
