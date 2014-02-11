---
title: 对 Rails Session 的理解
layout: post
guid: PNtFo1NMAoCl
tags:
  - Rails
---

<span class="image-800">![session]({{ site.url }}/media/files/2014/feb/09-session.png)</span>

为了理解session，我想像了一个场景：

老年痴呆患者是没有记忆力的，我告诉他我的名字，并热情的和他打招呼。可第二次会话时，他早已忘记我的名字。

HTTP 协议也是如此，没有状态的，多个请求之间毫无关联。

## Session的用途

session 是一串 Hash，保存在浏览器的Cookie文件中。

1. 把 user id 放到 session 中。
2. 每次浏览器发起请求时捎带着cookie信息。
3. 服务器获取 session 中的user id
4. 服务器根据 user id 把当前用户拎出来。


## 禁忌

1. 不要在session中保存对象。

    cookie 只能保存4kB的信息，一个对象就可能把它撑爆了。

2. 不要在session中保存重要的数据，因为：

    1. 用户关闭浏览器或清空cookie后，这些重要的数据会丢失。
    2. session在客户端保存，用户可以读取这些信息。
