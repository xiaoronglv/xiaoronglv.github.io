---
title: 调试 Ajax 的思路
layout: post
guid: ZWKRWYcd3poU
tags:
  - 
---

这周要用Ajax实现级联菜单的效果，在实现的过程中遇到一个个的问题。由于对前端工具不太熟悉，所以在填坑时，基本靠猜。

光明开始传授我「调试Ajax大法」，回家后我又学习了 Railscast 的一个课程 [jQuery & Ajax](http://railscasts.com/episodes/136-jquery-ajax-revised?view=asciicast)。大体知道了调试 Ajax的套路。



1. 使用 Console 检查 javascript 的语法错误

    JS这门奇葩的语言的奇葩语法把我绕到云里雾里，漏掉花括号{}已经成为家常便饭。在调试Ajax之前，先通过 Console 检查我的拼写是否有误。
    
    <span class="image-800">![network]({{ site.url }}/media/files/2013/nov/24-console.png)</span>

2. 使用 Network 检查网络请求

    通过Chrome Network， 可以监测浏览器发起的所有请求。我们可以找到属于Ajax的那一个请求，然后抽丝剥茧，排插问题。

    <span class="image-800">![network]({{ site.url }}/media/files/2013/nov/24-network.png)</span>

3. 检查 Headers

    Headers中包含的参数很多，我看的比较多的就是 Status Code, Query String Parameter.

    * Query String Parameter：浏览器发起某个请求时，包含的参数。
    * Status Code：服务器状态码，不同的状态码有不同的含义，大家都喜欢 [200](http://zh.wikipedia.org/wiki/HTTP状态码)。

4. 检查 Response

    Response是服务器返回给浏览器的内容，可以是一段html代码，也可以是一段js。

    <span class="image-800">![network]({{ site.url }}/media/files/2013/nov/24-response.png)</span>
    
通过以上四步，大部分问题就可以搞定。


## 总结

定位问题的思路是最重要的，也是最难通过自学获取的，这时候小伙伴的一句指点胜过十年书。



