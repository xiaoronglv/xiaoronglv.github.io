---
title: 如何记忆 belongs_to
layout: post
guid: Q5Vzelr5G6gO
tags:
   - Rails
   - Ruby
---

belongs_to 用起来很简单，但是记忆它的参数是和表结构是个挺费神的事情。你能不假思索的回答这些问题吗？

1. 当 A belongs_to B 时，他们的关系字段保存在哪个表呢？

2. primay_key 是什么参数？

3. foreign_key 是什么参数？


经过不懈努力，我总算把这个知识点用个例子形象化了，瞬间记忆，不再遗忘。


有10个奴隶属于1个主人，奴隶的身上都烙上了主人的徽章。

* 徽章是主人身份的唯一标识: primary_key
* 徽章对于奴隶来说，是外来标记: foreign_key
* 徽章是烙在奴隶身上的( 保存在 slaves 的表中，字段为master_id)

这样就一口气把各种乱七八糟的参数都记住了。

<span class="image-800">![Feet Washing]({{ site.url }}/media/files/2014/May/12-belongs_to.png)</span>
