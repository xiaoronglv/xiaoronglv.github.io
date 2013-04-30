---
title: 被遗弃的OAI
layout: post
guid: cuBaiwBiiMKp6
tags:
  - 
---

<span class="image-1200">![](/media/files/2012/mar/23.png)</span>

## OAI产生的背景

每个图书馆有不同的内容,如何将某一个服务器上的电子文档能被全世界的获取到? 
 
如何在分布的小服务器(arichive center)之间实现信息的有效检索和互操作?  

1999年10月,美国圣达菲着了了一次刮目与促进电子文档发展的研讨会,定义了一个标准 OAI,其目的是形成一个使用收获元数据机制来提高不同数据仓库之间的互操作能力的协议.

## 角色

1. data provider (元数据提供者,Repository/archive)
2. service provider (服务提供者)
3. 注册机构

## OAI协议运作模式

1. 数据的提供方与服务提供首先要在注册服务器进行注册,确保两者都符合OAI协议的规范;
2. 数据提供方(data provider)将元数据存储在支持OAI协议的服务器(Repository)上;
3. 服务提供方(service)通过OAI协议请求命令向data provider发起请求,接收回应.所有的请求和回应方式为Http的Get与Post操作.


## 元数据格式

每个数据提供方可能存在着不同的元数据格式,必须统一为都柏林元数据标准(dublin core metadata).

评语
OAI只是组织数字对象的元数据,并不涉及数字对象本身.对用户来讲,用处基本不大.
相同的资源存储在世界各地,数据冗余很严重,通过共享meta信息,妄想实现基于实现全球数据网络整合,本身就是一个弯路,完全可以将数据保存在云端,不同的图书馆根据等级获取不同的数据权限.