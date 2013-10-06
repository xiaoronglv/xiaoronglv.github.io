---
title: Dxy 5 - Java之重
layout: post
guid: mFlq1DpfuDPk
tags:
  - 
---

2012年底的时候，我们决定做一款医学词典。

原因很简单，医脉通的iOS版「全医学英汉大词典」实在是是太烂了。这么烂的软件卖这么贵，居然还有人买。

按计划先出web版，再出iOS版。大概用了1周时间，设计稿就出来了，数据也准备妥当。心想春节之前折腾出来，过个好年。

词典的后端Sean，是个百里挑一的Java程序员，十分优秀。他花了一周的时间写框架，两周的时间写逻辑套页面。加上七七八八的测试，年前发布无望。

相对于Java的慢，Sam用 [Ruby on Rails](http://rubyonrails.org) + [ElasticSearch](http://railscasts.com/episodes?utf8=✓&search=ElasticSearch) + [Twitter Bootstrap](http://getbootstrap.com) + [Devise](http://railscasts.com/episodes/209-devise-revised) ，3个小时就把词典的web版本搞定了。

Java Vs Ruby，悬殊的开发速度，第一次让我有了感性上的认识。

Paul Graham在*黑客与画家*中描述，Lisp是他的秘密武器。当他看到竞争对手还在使用错误的语言做web开发时，就十分高兴。因为竞争对手的开发速度太慢了，永远跟不上他的节奏。

Web开发，唯快不破，快不过Rails。

使用 Ruby 做开发的团队，出生时就已经具备了敏捷的基因。