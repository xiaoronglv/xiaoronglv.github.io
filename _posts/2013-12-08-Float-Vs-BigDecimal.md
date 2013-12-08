---
title: Float Vs BigDecimal
layout: post
guid: abYcQMjG57vR
tags:
  - Ruby
---

什么是 Float？
什么是 BigDecimal？
他们的区别是什么？

我们先从一段代码说起：
<pre><code>
1.9.3-p448 :069 > 0.1.class
 => Float 
 
1.9.3-p448 :069 > 0.2.class
 => Float 

1.9.3-p448 :068 > 0.1 + 0.2
 => 0.30000000000000004 
</code></pre>

计算结果为一个近似值 0.30000000000000004，为什么？

这要从 Float 的存储说起。

## Float 的存储

Float是按照2进制来存储的。

0.1 保存为 Float 格式时，它就不再是精确的 0.1，而是最接近它的一个二进制。

## 愚蠢的格式？

既然 Float 有误差，为什么还有人使用？

因为它占用空间小, 运算速度快, 精度固定。

## 涉及到钱，必须准确无误

我们想象一个场景，用户在付款时看到我们的系统 0.1 + 0.2 = 0.30000000000000004，他肯定骂一句「What's the fuck! 」，然后拍拍屁股走人，再也不会购买我们的产品。

所以涉及到钱，不允许出现一丁点错误。使用高精度的 BigDecimal 就很有必要了。

decimal 是用字符串存的, 或者说是10进制, 占用空间大, 运算速度慢, 但是计算结果是精确值，不会有误差。

<pre><code>
1.9.3-p448 :082 > BigDecimal.new('0.1') + BigDecimal.new('0.2')
 => #<BigDecimal:7fa1bca4c468,'0.3E0',9(27)>
</code></pre>

## 总结

1. decimal 10进制保存, 占用空间大, 运算速度慢, 不存在 [rounding error](http://floating-point-gui.de/errors/rounding/)

2. float 2进制保存, 占用空间小, 运算速度快, 存在rounding error


## 参考资料

1. [Ruby China: decimal和float的具体区别](http://ruby-china.org/topics/8351)

2. [0.1 + 0.2 = 0.30000000000000004](http://floating-point-gui.de/basic/)

3. [Rounding Error](http://floating-point-gui.de/errors/rounding/)