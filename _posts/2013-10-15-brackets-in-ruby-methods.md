---
title: Ruby方法的括号
layout: post
guid: mFCz5ywW9xBl1
tags:
  - Ruby
---

<pre>
<code>generate_xls name: "导出订单"</code>
</pre>

在使用 Ruby 方法时，省略括号会让代码简洁，但是也存在很多问题

1. Ruby必须先读完整行代码，才知道 name: "导出订单" 是个参数

2. 代码不够直白，code reviewer 会十分困惑。 是参数吗，还是其他？

3. 如果和 if 搭使用，优先级会变的不够清晰。

	<pre><code>generate_xls name: "导出订单" if name.empty?</code></pre>

## 什么情况下保留括号？

1. 自定义方法的调用时，括号让代码更直白；

2. 搭配 if 等优先级判断，括号让代码更有效率。

## 什么情况下可忽略括号？

约定俗成的方法调用时，可以忽略括号。

<pre><code>
assert_not_nil assigns(:order)  
assert_response :success
</code></pre>