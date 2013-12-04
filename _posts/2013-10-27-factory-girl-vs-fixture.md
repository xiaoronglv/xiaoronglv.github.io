---
title: Factory Girl Vs Fixture
layout: post
guid: dkpgtCUYgMbw1
tags:
  - Ruby
  - Rails
---

<span class="image-800">![spinner girl]({{ site.url }}/media/files/2013/oct/26-spinner-girl.png)</span>

在测试 Rails 时，我们需要准备测试数据，在这方面比较主流的工具有两种：

1. Fixture
	
	Rails 默认方式

2. [Factory Girl](https://github.com/thoughtbot/factory_girl)

	Thoughtbot公司开发的一套工具
	
一图胜千言，制作了两张流程图，以此表述 Fixture 与　Factory Girl 在准备测试数据的方式差异。

## Fixture

<span class="image-800">![fixture]({{ site.url }}/media/files/2013/oct/26-fixture.png)</span>

Fixture 是如何准备测试数据的呢？

1. 第①步霸王硬上弓，将数据塞到数据库 （Database） 
2. 第②步根据数据库中的数据创建对象（Object）

可真实世界中，Rails 的流程更多是这个样子：

1. create an unsaved object
2. save
3. run calls in ActiveRecord, such as validates
4. success or fail

两者大相径庭，**Fixture 无法很好的测试 ActiveRecord 中的回调**（callback）。

如果要实现回调函数，需要在Fixutre中插入一堆代码。这太没有美感了，有洁癖的程序员都不会干这种事情。

此外，在 Fixture 中**维护两个对象之间的关系也是一件很费劲的事情。**我们要在YAML文件中根据id创建关系，一不小心就会把自己绕进去。假如存在 User、Topic 两个类，他们之间的关系如此：

	# topic belongs_to :user
	# user has_many :topics
	# 稍候补上代码

## Factory Girl

<span class="image-800">![factory girl]({{ site.url }}/media/files/2013/oct/26-factory.png)</span>

Factory Girl 的准备方式与真实世界更相似。

1. 第①步生成的对象可以是saved instances, unsaved instances；
2. 可以在第②步执行ActiveRecord的各种回调
3. 打理对象之间的关系十分方便

	<pre><code>
	peter = create :user  
	topic = create(:topic, user_id: peter) #测试时创建关系，更加flexible
	</code></pre>

## 总结

国内各种开源项目几乎都在用 Factory Girl，这么多人选择她，你也值得拥有。

她还有很多很实用的功能，比如继承、特征等等，请参考官方文档。

## 相关阅读

1. [Juanito: FactoryGirl 粗浅介绍](http://ruby-china.org/topics/3777)
2. [Factory Girl wiki](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md)