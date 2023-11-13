---
title: "代码风格：Early Return"
layout: post
guid: kVfxFsXzqXQirR2Pf
date: 2022-07-28 14:18:40
tags:
  -
---

同事提醒我代码有缺陷，我于是翻看代码，看了半天，还是没看懂，真尴尬，这代码是我起一年前写的。注释也在，但就是看不懂。

当时我用了一种 early return 这种写作风格，在方法中，罗列各种不需要的因素，然后提前中止方法的运行。


```
def process_code
  # 如果是陈旧消息，则不处理
  return if stale?  
  #如果消息已经处理过了，也不处理。
  return if status == "processed" 
  #如果
  return if type != "e-verify" 

  update(code: 'success") if payload['code'] == 'success'
end
```