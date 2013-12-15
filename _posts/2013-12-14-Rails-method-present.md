---
title: present?
layout: post
guid: abO4ya6ikxpn
tags:
  - Rails
---

我经常会在 controller 写这种丑陋的代码：

<pre><code>
unless @labels.blank?
    ...
end
</code></pre>

unless 加 blank?，双重否定，读起来好拗口啊！

## not_blank?

假如世界上有个 not_blank? 方法该有多好啊，我的小脑袋就不用绕来绕去了。

可惜这个方法不存在。

<pre><code>
if @labels.not_blank?
    ...
end
</code></pre>

## present?

幸好我有一双发现美的眼睛，偶尔看到别人更直白的处理方式。

<pre><code>
if @labels.present?
    ...
end
</code></pre>

present? 是一个 Rails 方法，源码：

<pre><code>
# File activesupport/lib/active_support/core_ext/object/blank.rb, line 18
def present?
  !blank?
end
</code></pre>

> An object is present if it's not blank?.
> 

这个方法够直白，妈妈再也不用担心我的脑容量了。

