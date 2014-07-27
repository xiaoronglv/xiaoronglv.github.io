---
title: Ruby Asterisk 和 Ampersand
layout: post
guid: EPuOcDwq0Kp
date: 2014-07-20 15:00:00
tags:
   - 
---

Ruby 两个符号 * 和 & 经常帮我搞得晕头转向，今天梳理了他们的两个两个最主要的使用场景：

* 调用方法时
* 定义方法时

## Asterisk \*

在调用方法时，若在参数中加上 \*，表示：先拆分数组，再调用方法

    a = ["Jesus", "Loves", "U"]

    # 以下两种效果一致
    p(*a)
    p(a[0], a[1], a[2])



在定义方法时，若在参数中加上\*, 则表示: 方法内部会把所有的参数转化为一个数组。

    def say_hello(*a)
      puts a
      puts a.class
    end

    c, d, e = "I", "will", "be", "merged"

    # c, d, e 会被合并为一个数组 a
    > say_hello(c, d, e)
    > I
      will
      be
      merged
      Array

## Ampersand \&

调用方法时, \& 把一个 proc 对象转化为块，并传递给方法。

    my_proc = proc { "Jerusalem" }


    def say_hello(greeting)
      "#{greeting} #{yield}"
    end

    # 以下两种写法相似
    say_hello("hello", &my_proc)

    say_hello("hello"){"Jerusalem"}


在定义方法时，\& 方法内部会把传递进来的块转化为 proc。

    def say_hello(greeting, &jerusalem)
      # It's a proc
      p jerusalem.class
    end
