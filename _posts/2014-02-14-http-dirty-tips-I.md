---
title: HTTP 报文结构记忆
layout: post
guid: mK064sOnr9mt
tags:
  - Memory
---

将知识影像化，然后放到[记忆宫殿](http://en.wikipedia.org/wiki/Method_of_loci)中。

记忆内容：[《HTTP权威指南》](http://book.douban.com/subject/10746113/)第3章 HTTP报文

## Request Message

勾勒人物：男人

1. 一个朝鲜男人, 顶着个很浅的盆子

    很浅的盆子：start line
    
2. 盆子中有动词、面包屑、http和版本号

    动词：http动作
    面包屑：相对url地址
    http+版本号
    
3. 男人的头很大，嘴里叼着一个曲奇（cookie），眼神迷离，脑子中在想象渴望的少女。

    1. 头很大：header 携带很多信息
    2. 女孩的家在哪里：Host
    3. 接受哪种类型的女孩：Accept
    4. 曲奇饼干：Cookie
    
4. 男人的肚子平时很小，对女孩想入非非时肚子变大。

    1. 小肚子：request message一般主体为空，所以肚子很小。
    
    2. 大肚子：当男人有一肚子精液，准备post时，肚子（body）很大。


## Response Message

勾勒任务：女人

1. 一个朝鲜女人，顶着一个很浅的盆子

    很浅的盆子：start line
    
2. 她的脑袋在想，我该如何回复那个男人？

    脑袋：header
    
    脑袋中想的东西：set cookie，资源类型，编码
    
3. 她的肚子被人搞大了

    肚子：body
 
   
    