---
title: How Minitest works
layout: post
guid: zktmnl942fbc6d2huxpb
date: 2015-10-31 19:59:20
tags:
  - Ruby
---

以前读源码时，总是急不可耐的去翻别人美轮美奂的代码，走马观花的看一遍。事后印象不深刻，不知道自己和作者的差距主要在哪里。因为印象不深刻，也没有把一些好的设计运用到自己的项目中，水平提高很慢。

我的东家 SAP 面向的是企业客户，不求最快，只求稳健。每个季度是一个开发周期，最后一月修 bug ，空闲时间较多。在时间充沛的情况下，我转换了读源码的思路： **自己先尝试实现这个项目（或写出大体思路），再看源码**。 

实践流程如下：

**事前思考**

General Question

* 如何抽象？
* 如何分层？
* 配置文件的组织方式？production/stage/development
* 该如何做

Some Special Question

* 如何处理信号？
* 如何把任务分发到多个进程？
* 如何把子进程的的输出汇总到 master worker?


**事后比较**

* 我没做到的，作者如何做到了？
* 我做到的，作者比我高明在哪个地方?

这与走马观花最大的区别是：会就是会，不会就是不会。立马就知道自己哪方面比较薄弱，再也无法跳过。一开始骨头比较难啃，但是越往后越觉得有趣。

当然，在挑开源项目时也要考虑自己的能力，挑一些垫起脚来可以实现的，而不要挑那些复杂度远超过自己能力的。

拿最近我在读 Minitest 举例，去掉注释他的代码一千多行，比较简洁，里面也运用了很多元编程技巧。

## 如果让我从头写一个 Minitest，我会怎么设计呢？

1. Require test files (xx_test.rb).

2. Collect all the subclasses of Minitest::Test.

3. Collect all test_methods, transform to test jobs.

4. Run every test, output results.

思考完框架后，开始丰满细节，但其中三个细节，想破脑袋也不知道如何实现。

![](/media/files/2015-10-31-how-to-draw-a-horse.jpg)

## 细节

这段代码定义了一个测试类，终端执行 ruby test_first.rb 不会出现任何 output，进程的 lifecycle 转瞬即逝。

```
# test_first.rb
class TestStruct < Minitest::Test
  def test_struct
    assert_equal "chillout", Struct.new(:name).new("chillout").name
  end
end
```

然而在头部加上 require "minitest/autorun"，它却摇身一变成为测试文件。

```ruby
# test_second.rb
gem "minitest"
require "minitest/autorun"

class TestStruct < Minitest::Test
  def test_struct
    assert_equal "chillout", Struct.new(:name).new("chillout").name
  end
end
```

麻雀虽小，五脏俱全，在终端里执行 ruby test_second.rb 可以输出测试结果。

```
$ ruby test.rb
Run options: --seed 3152

# Running:

.

Finished in 0.001035s, 966.3890 runs/s, 966.3890 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
```

![](/media/files/2015-10-30-magic.gif)


好奇的程序员立马就被钓起了胃口，Minitest 偷偷摸摸的在这段代码背后做了什么。

1. 它是如何收集所有的测试类？
2. 它是如何把 TestStruct 的实例方法如何转化为一个个 test job？
3. 什么时候 test job 被执行了？

## 问题1：Minitest::Test 是如何收集所有的测试类？

自己实现代码丑陋无比，承认无能后，我开始翻阅源码。作者使用了一个元编程的钩子(Meta Programming Hook) `self.inherited` 来解决问题。

![](/media/files/2015-11-01-minitest-self-inherited.jpg)

所有的 Test case class 都继承自 Minitest::Runnable。 当你定义一个个测试类时，就会触发 self.inherited 钩子，从而把 Test case class 都塞到了 Minitest::Runnable 的类变量 [@@runnables](https://github.com/seattlerb/minitest/blob/f771b23367dc698586f1e794eae83bcb905fa0d8/lib/minitest.rb#L233-L236) 中，我们写的所有测试类最后都会汇总到这里。

```ruby
module Minitest
  class Runnable
    def self.inherited klass
      self.runnables << klass
      super
    end
  end
end
```

> 题外话，Ruby 常见的元编程钩子有：
> 
> * inherited, 创建子类时被触发。
> 
> * included: module 被 included 时触发。
> 
> * extended，module 被 extended 时触发。


## 什么时候 run test job?

```ruby
# test_second.rb
gem "minitest"
require "minitest/autorun"

class TestStruct < Minitest::Test
  def test_struct
    assert_equal "chillout", Struct.new(:name).new("chillout").name
  end
end
```

这段代码中并没有 run test 的逻辑，但 Minitest 却把这事做了，到底是什么时候做的呢？

谜底是：在进程退出的时候。

[Kernal#at_exit](http://ruby-doc.org/core-2.0.0/Kernel.html#method-i-at_exit) 方法可以提前声明进程退出时要做的事情，在进程退出时触发，很多 Gem 都在使用，Capybara 使用它来关闭浏览器， Sinatra 使用它在最后跑 Application。

Document: Converts block to a Proc object (and therefore binds it at the point of call) and registers it for execution when the program exits. If multiple handlers are registered, they are executed in reverse order of registration.

以下是 at_exit 的简单范例

```ruby
# exit.rb
puts "step1"
at_exit do
  puts "step2"
end
puts "step3"
```

这段代码 ruby exit.rb 的输出为

```
step1
step3
step2
```

可以看到 step2 最后才被执行。

[Minitest.autorun](https://github.com/seattlerb/minitest/blob/master/lib/minitest.rb#L45-L59)，详细罗列了进程退出时的 TODO 清单。

```ruby
# Registers Minitest to run at process exit
def self.autorun
  at_exit {
    next if $! and not $!.kind_of? SystemExit

    exit_code = nil

    at_exit {
      @@after_run.reverse_each(&:call)
      exit exit_code || false
    }

    exit_code = Minitest.run ARGV
  } unless @@installed_at_exit
  @@installed_at_exit = true
end
```

但这种设计方式太晦涩了，精妙的不太正常。就像一部戏剧把高潮写到了页脚，给人的感觉怪怪的。


## Reference

1. [Are we abusing at_exit?](http://blog.arkency.com/2013/06/are-we-abusing-at-exit/)

2. [Hitchhiker’s Guide to Metaprogramming: Class/Module Hooks](http://www.sitepoint.com/hitchhikers-guide-to-metaprogramming-classmodule-hooks/)