---
title: Rails Session II
layout: post
guid: 0NIDabTNPcg4
tags:
  - Rails
---

<span class="image-800">![cookie]({{ site.url }}/media/files/2014/feb/11-cookie-1.png)</span>

## Cookie的数据结构

1. Cookie 中的值都是以key: value的形式展示的。
2. 明文展示，在客户端一览无余。

## Cookie文件是动态的

1. 客户端发起每次请求时，会捎带着cookie。（Request）
2. 服务器响应请求时，重新返回新的Cookie数值。（Response）

## Session 寄人篱下

session信息以键值对的形式保存在cookie中。Rails session 命名习惯是_app_session.

假如我的项目名为food，则session key的名字就是_food_session

<span class="image-800">![cookie]({{ site.url }}/media/files/2014/feb/11-cookie-2.jpg)</span>

## Session 中的内容

session字符串可以拆分为两部分：

<span class="image-800">![cookie]({{ site.url }}/media/files/2014/feb/11-session-2.jpg)</span>

1. 正文

    已被base64编码，很轻松就可还原为一个hash。

        1.9.3p448 :001 > require 'base64'
        => true
    
        1.9.3p448 :001 > str = "BAhJIgGDeyJzZXNzaW9uX2lkIj0+IjAwZDllY2FlYWZmZGEyMzIzMjRhZmZh\nZThjMDYzMzcwYWJiMDgiLCAidXNlcl9pZCIgPT4gIjEzMTM0IiwgIl9jc3Jm\nX3Rva2VuIj0+ImNlbzRqMm9Jck9TM2dMRFBzZ3UzY0VISGV1WnlIczE2NlNp\nIn0GOgZFVA==\n"

        => "BAhJIgGDeyJzZXNzaW9uX2lkIj0+IjAwZDllY2FlYWZmZGEyMzIzMjRhZmZh\nZThjMDYzMzcwYWJiMDgiLCAidXNlcl9pZCIgPT4gIjEzMTM0IiwgIl9jc3Jm\nX3Rva2VuIj0+ImNlbzRqMm9Jck9TM2dMRFBzZ3UzY0VISGV1WnlIczE2NlNp\nIn0GOgZFVA==\n"

        1.9.3p448 :002 > new_str = Base64.decode64(str)
    
        => "\x04\bI\"\x01\x83{\"session_id\"=>\"00d9ecaeaffda232324affae8c063370abb08\", \"user_id\" => \"13134\", \"_csrf_token\"=>\"ceo4j2oIrOS3gLDPsgu3cEHHeuZyHs166Si\"}\x06:\x06ET" 

        1.9.3p448 :003 > Marshal.load(new_str)
    
        => {"session_id"=>"00d9ecaeaffda232324affae8c063370abb08", "user_id" => "13134", "_csrf_token"=>"ceo4j2oIrOS3gLDPsgu3cEHHeuZyHs166Si"} 


2. digest

    正文只是简单的编码，并未加密的。如果用户(user_id = 13134)篡改了正文(user_id = 2)，就有访问其他用户信息的风险。

    为了防止cookie被篡改，每一个session字符串的尾巴上都带着一个服务器生成的digest。

    若用户的content与digest不匹配，服务器就知道这个cookie是伪造的。

3.  digest如何生成

    在Rails中，secret保存在 config/initializers/secret_token.rb 文件中。

        YourApp::Application.config.secret_key_base = '49d3f3de9ed86c74b94ad6bd0...'
    
    digest生成规则：content + secret token => digest
    
    这个secret token是机密文件，一旦被坏人获取，就可以轻松的伪造cookie（很多菜鸟开源代码时往往犯这个低级错误）。

## 参考资料

1. [Ruby on Rails Security Guide](http://guides.rubyonrails.org/security.html)

2. [Marshal](http://www.ruby-doc.org/core-2.1.0/Marshal.html)

    The marshaling library converts collections of Ruby objects into a byte stream, allowing them to be stored outside the currently active script. This data may subsequently be read and the original objects reconstituted.

3. [Base64](http://ruby-doc.org/stdlib-2.1.0/libdoc/base64/rdoc/Base64.html)

    Base64是一种基于64（A-Z、a-z、数字0-9 ，‘+’,‘/’）个可打印字符,来表示二进制数据的表示方法，基本思想是把所有字符都转化到这64个打印字符，方便流通。