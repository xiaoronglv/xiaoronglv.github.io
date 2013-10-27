---
title: Ruby的开放类
layout: post
guid: e3LN6KaOOmnY1
tags:
  - Ruby
---

Ruby的开放类（open class）是一个非常有用的动态特性，允许向已加载的类增加方法。

	class String
 	  def foo
 		 "foo"
 	  end
	end 

\> puts "string".foo  
\> "foo"

先前已经定义好的String类，我添加了一个实例方法 String#foo，随后就可立即调用了。