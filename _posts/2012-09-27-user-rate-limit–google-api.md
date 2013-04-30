---
title: User rate limit – Google API
layout: post
guid: uD9P4iAGJh5L103
tags:
  - 
---

在我们开发产品时，遇到一个需求：根据ISSN号获取书籍标题、作者、出版社、页码、简介等信息。在stackoverflow上Obvio已经对各种解决方案进行了评估。

使用105本书籍的ISBN号在各种API上做测试：

google book api：可查到77本
librarything：可查到70
GoodReads：可查到64本
Open Library Books：55本
于是乎，我最终选择了google api。


## 策略

1.首先根据issn检索目标书籍

https://www.googleapis.com/books/v1/volumes?q=isbn:9781607950301

google会返还一串jason字符串，里面包括符合条件的的数量，id，标题，作者等信息。查看Google的详细文档


    {
    "kind": "books#volumes",
    "totalItems": 1,
    "items": [
    {
    "kind": "books#volume",
    "id": "_f5aAAAAIAAJ",
    "etag": "QYBkWXuiSy0",
    "selfLink": "https://www.googleapis.com/books/v1/volumes/_f5aAAAAIAAJ",
    "volumeInfo": {
    "title": "知性改进论",
    "subtitle": "并论最足以指导人达到对事物的真知识的途径",
    "authors": [
    "Benedictus de Spinoza"
    ],
    "publishedDate": "1960",
    "description": "据C.Gebhardt 校订拉丁文本并参考C.Gebhardt 德文译本译出",
    "industryIdentifiers":
    ..............
    ..............
    
2.根据书籍的id获取所有信息

第一次查询返还了书籍的大部分meta信息，但是还是有些遗漏，比如：出版商。

于是我通过id（书籍在google中的惟一id）获取书籍的详细信息，这一次把该书籍所有的信息都拿了过来。

https://www.googleapis.com/books/v1/volumes/_f5aAAAAIAAJ

## 碰到的问题

刚上线时并未发现问题，推广宣传后，用户请求API的次数骤增，Google的API开始返回403错误，这一天来的好早。
    
    {
    "error": {
    "errors": [
    {
    "domain": "usageLimits",
    "reason": "userRateLimitExceededUnreg",
    "message": "User Rate Limit Exceeded. Please sign up",
    "extendedHelp": "https://code.google.com/apis/console"
    }
    ],
    "code": 403,
    "message": "User Rate Limit Exceeded. Please sign up"
    }
    }
    
于是又花了一个上午读Google Book API的文档。原来普通的开发者在使用Google API时要申请一个API key，每个key每天可请求1000次。到目前为止，已经找到问题的原因了，开始解决问题。

## 解决问题

1.申请开通API ，获取key

google 所有的api在[api console](https://code.google.com/apis/console/)中统一管理，比如地图、google plus、google drive等。我只打开了google book api。如果你的产品还需要其他API，可以顺便打开。

2.重新构造接口的链接，把key在参数上

2.1 根据issn查询

    https://www.googleapis.com/books/v1/volumes?q=isbn:7100020247&key=AIza***********

2.2 根据返回的id获取书籍所有的信息

    https://www.googleapis.com/books/v1/volumes/_f5aAAAAIAAJ?key=AIza********

书籍的信息可以正常获取了，问题解决了。

如果你的网站有更大的需求，可以填写一个表单，申请更高的访问配额。