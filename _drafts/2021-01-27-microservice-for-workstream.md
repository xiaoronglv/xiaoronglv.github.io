---
title: "workstream Microservice 构建之路"
layout: post
guid: JscVavSFHSrYe9IyRGR
date: 2021-01-27 12:34:52
coffee:
tags:
  -
---

## 目标是什么？


**业务量一个脑袋就可以装下，减少维护的负担。**

Junior developer 不用关心上游应用的逻辑，只需要处理好 service 内部的逻辑就可以了。


**自治**

- 不被单一的编程语言限制开发速度。我们可以招聘大量的外包程序员来迅速构建足够多的集成服务。
- 如果我们团队内的人对 Golang / Python / Java 感兴趣，也可以在单个的小的服务中先做尝试。



## 如何管理消息的 schema？

contract 不能变来变去

将来的时候，要支持多种语言，不能随便编




## Event Streaming 的技术选型


1. SNS fanout + SQS queues


2. Kinesis

Price

3. 



## 从头实现一个框架，需要考虑的因素

部署

Event <-> Consumer 的 Mapping 的维护



## 选用别人