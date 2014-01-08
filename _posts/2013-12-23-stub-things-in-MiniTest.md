---
title: How to stub things in MiniTest
layout: post
guid: EicQG3UZD3mU
tags:
  - Rails
---

## Instance Method

<pre><code>
require 'mocha'    
Books.any_instance.stubs(:title).returns("Pride and Prejudice")
</code></pre>

test case 调用一本书的书名时，返回「傲慢与偏见」

## Class Method

<pre><code>
Book.stubs(:count).returns(50)
</code></pre>

test case 调用 Books 的总数时，返回50
