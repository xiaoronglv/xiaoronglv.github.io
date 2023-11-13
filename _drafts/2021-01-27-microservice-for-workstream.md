---
title: "workstream Microservice 构建之路"
layout: post
guid: JscVavSFHSrYe9IyRGR
date: 2021-01-27 12:34:52
tags:
  -
---

## 目标是什么？

2周就可以完成一个 integration 的开发。

https://www.zendesk.com/apps/directory


## 新框架应该满足的特性

**使用通用技术栈，方便找人**


**尽量选用成熟的解决方案，不要重复造轮子。**

- https://github.com/zendesk/ruby-kafka
- https://github.com/zendesk/racecar


**提供项目模版 rails template，方便 developer 在2分钟内把项目轮廓搭好**


**每个integration 代码量足够小，一个脑袋就可以装下，减少维护的负担。**

Junior developer 不用关心上游应用的逻辑，只需要处理好 service 内部的逻辑就可以了。


**自治**

- 不被单一的编程语言限制开发速度。我们可以招聘大量的外包程序员来迅速构建足够多的集成服务。
- 如果我们团队内的人对 Golang / Python / Java 感兴趣，也可以在单个的小的服务中先做尝试。



## 子任务

### 1. 如何管理消息的 schema？

contract 不能变来变去

将来的时候，要支持多种语言，不能随便变

https://github.com/helloworld1812/Protocol



### 2. Event Streaming 的技术选型


1. SNS fanout + SQS queues


2. Kinesis 
	- Price
	- stream management

3. Kafka


### 3. 主仓库和 microservice 之间的鉴权

1. JWK

2. 非对称签名的 JWT



### 4. 部署


### 5. Event <-> Consumer 的 Mapping 的维护

