---
title: "Feed 流设计(一)：如何对多态内容进行抽象？"
layout: post
guid: WocDzcY4u4jdq9vH6e
date: 2018-09-25 09:14:46
coffee: y
tags:
  - Programming
---

![](/media/files/2018/2018-09-25_15-13-00-screenshot.gif)


Facebook 是社交领域的标杆产品，也是一个不存在的网站。登录 Facebook，用户可以看到「好友分享了哪些内容」，「对哪些事情点了赞」，「分享了哪些书」等等。用户可以不断的往下滑动，只要用户想看，就没有底，没有尽头，这种产品交互的方式叫 「Feed 流设计」。

Feed 的意思是「喂养」，把信息比作饲料，把用户比作动物。如果它想吃，就可以源源不断的刷下去，把时间耗费在里面。微信朋友圈，微博，Facebook，Linked，花瓣都是「Feed流」设计。

Feed流中常见的内容有哪些呢？

1. Ryan 分享一本书《21世界的管理挑战》
2. Ryan 上传了一个视频「带儿子去吃烤鸭」
3. Ryan 发布了状态「我今天感觉不错」
4. Ryan 分享了一张图片「带儿子在上海交大遛弯」

![](/media/files/2018/2018-08-28-demo.jpg)


SAP 企业级应用 Jam 的首页也是采用 Feed 流设计，它包含了一些针对企业市场更复杂的事件。

- 公司发布了公司公告。
- 小组管理员发表了群公告。
- 同事 Tony 创建/更新/删除/评论了文档。
- 同事 Sam 创建了一个公开活动的日历


## 针对 Feed 流设计，在产品开发时该如何建模？

1. 被抽象内容多态，要考虑未来的扩展性。
2. Feed 流中出现的内容不是资源的当前状态的展现，而是对状态转换(Transition of state)的展示。

在以下部分，我会探讨一种基于事件的建模方案。这种方案经历了拥有1000+万用户的企业产品的测试，在现实世界中是真实有效的。

Martin Fowler 在 [Audit Log](https://martinfowler.com/eaaDev/AuditLog.html) 一文举例，当用户更新手机号时，他不仅关心「用户的新手机号是什么」(current state)，还关心「用户的手机号从A更新为B」，他对后者做了抽象，称之为审计日志(Audit Log)。

他的[Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) 模式也是基于事件的另一种设计范式。

## 一种基于事件的设计模式

Feed 不就是对事件的描述吗？我们可以换个角度，从事件的角度来看待 Feed 流。

| Feed 中的内容 | 基于事件的视觉 |
|:--|:--|
| Ryan 上传了一个视频「带儿子去吃烤鸭」 | `videos` 表插入了一条记录，id 为 1000， user_id 为 1 |
| Ryan 分享了一张图片「带儿子在上海交大遛弯」 | `images` 表插入了一条记录，id 为 1331，user_id 为 1  |
| Tony 删除了文档 | `wiki` 表中 id 为 433 的一条记录，属性 `is_deleted` 状态从 false 变为 true，user_id 为 3  |
| Sam 创建了日历 「周三团队聚餐」 | `calenda_events` 中插入一条记录， id 为 123，user_id 为 4  |

## 事件有哪些属性

Feed 中每个事件是对某个资源的状态变化(transition of state)，包含以下属性：

- table_name: 哪张表。(换句话说，哪种类型的资源)
- table_id: 哪条记录。
- column: 哪个属性
- operation: 什么操作？ e.g. update/insert/delete 
- old_value: 老的状态是什么
- new_value: 新的状态是什么

## 范式中的各个组件如何协作？

1. 资源的当前状态保存在各自的表中。e.g. videos, images, links, wikis, comments.
2. 当资源的状态变化时，把这一事件也保存下来，称之为 FeedEvent，它构成了 Feed。
		- CreateVideoFeedEvent 创建视频
		- DeleteVideoFeedEvent 删除视频
		- CreateWikiFeedEvent  创建文档
		- UpdateWikiFeedEvent  更新文档
		- DeleteWikiFeedEvent  删除文档
		- CreateCompanyAnnouncementFeedEvent 创建公司公告
3. 每个 FeedEvent 该如何渲染，放到 Concrete class 中去实现。
4. 每个 FeedEvent 会通过 `table_name`, `table_id` 引用到相关的资源。

![](/media/files/2018/2018-09-25-feed.png)

以上是对 Feed 内容的抽象，在后面的系列中，我会进一步阐述 Feed 分发，隐私策略等相关话题。


