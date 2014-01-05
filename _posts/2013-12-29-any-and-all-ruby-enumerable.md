---
title: Ruby 迭代器
layout: post
guid: Ub2OBE5ByJGd
tags:
  - Ruby
---

这周的一个业务场景：订单支付钱，需要检查用户订单中的商品状态。若包含下架商品，该订单不可被支付。

除了 Each，我对 Ruby 的其他迭代器一概不知。

一开始真不知道怎么写这段代码，难道判断所有的商品，将一堆 true 、false 塞到一个数组中，最后合并判断？

<pre><code>
# 这样写太丑了，简直有辱 Ruby 的名号。
a = true
products.each do |product|
  a = a && product.on_sale?
end
</code></pre>

后来翻了翻 Ruby 的文档，直接惊呆了。Ruby 有这么多好玩的迭代器！

## all?

所有的元素都必须为好人，返回的结果才是好人。

<pre><code>
%w[ant bear cat].all? { |word| word.length >= 3 } #=> true
%w[ant bear cat].all? { |word| word.length >= 4 } #=> false
[nil, true, 99].all?                              #=> false
</code></pre>

## any?

所有的元素中只要有一个是好人，返回的结果就是好人。

<pre><code>
%w[ant bear cat].any? { |word| word.length >= 3 } #=> true
%w[ant bear cat].any? { |word| word.length >= 4 } #=> true
[nil, true, 99].any?                              #=> true
</code></pre>


## 总结

<pre><code>
products.all? do |product|
  product.on_sale?
end
</code></pre>

只要有一个商品下架，返回的结果就是 false，这样写就漂亮多了。