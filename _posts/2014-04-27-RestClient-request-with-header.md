---
title: RestClient
layout: post
guid: ZQAcigE96Gqz
tags:
   - Rails
---

RestClient 的文档中写了几种常规的用法，但是没有每个参数的具体说明。

    require 'rest_client'
    
    RestClient.get 'http://example.com/resource'
    
    RestClient.get 'http://example.com/resource', {:params => {:id => 50, 'foo' => 'bar'}}
    
    RestClient.get 'https://user:password@example.com/private/resource', {:accept => :json}
    
    RestClient.post 'http://example.com/resource', :param1 => 'one', :nested => { :param2 => 'two' }
    
    RestClient.post "http://example.com/resource", { 'x' => 1 }.to_json, :content_type => :json, :accept =>   :json
    
    RestClient.delete 'http://example.com/resource'


其是就三个主要参数

    RestClient.post 
      'http://example.com/resource',  #url
      {:params => {:id => 50, 'foo' => 'bar'}}, #body
      from: "taobao", # header
      refer_user: "xiaoronglv" # header
      

* 第一个参数: 为 URL

* 第二个参数: 为 HTTP 报文的 body

* 第三个参数以及后面的多个参数，自动转化为一个 Hash，塞到 HTTP 报文的 Header 中。

## 鲜明的对比

这周一我需要在 RestClient 请求的 Header 中加一些自定义参数。这个 Gem 的文档写的过于简略，我google、stackoverflow 之后依然没找到合适的解决方法。

但是这问题在 @zgm 手里根本不算问题，半猜半读，很快就搞定了。

## 解决问题的思路

这件事情不大，但是很触动我。以前我解决问题的思路是：

1. Read Document
2. Google
3. StackOverflow
4. try, try again...
5. ask colleagues for help
6. **read source code**

现在应该增加一条：「阅读源码」，否则永远只会停留在初级菜鸟阶段。

感谢光明的提醒。

