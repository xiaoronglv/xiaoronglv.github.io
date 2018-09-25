---
title: "Feed 流设计之建模 (一)"
layout: post
guid: WocDzcY4u4jdq19vH6e
date: 2018-09-25 09:14:46
coffee: y
tags:
  - Programming


这是问答网站知乎的首页，在这个页面用户可以看到「好友关注了哪些有趣的问题」，「哪些问题有了新回答」，「哪些专栏发布了新文章」等等。用户可以不断的往下滑动，一页一页的内容自动会加载进来，只要用户想看，就没有底，没有尽头。这种产品交互的方式叫 「Feed 流设计」。

Feed 的意思是「喂养」，把信息比作饲料，把用户比作动物。如果它想吃，就可以源源不断的刷下去，把时间耗费在里面。微信朋友圈，微博，Facebook，Linked，花瓣都是「Feed流」设计。

Facebook 是社交领域的标杆，它的Feed流中包含哪些内容呢？

![](/media/files/2018/2018-08-28-demo.jpg)

1. Ryan 分享一本书《21世界的管理挑战》

2. Ryan 上传了一个视频「带儿子去吃烤鸭」

3. Ryan 发布了状态「我今天感觉不错」

4. Ryan 分享了一张图片「带儿子在上海交大遛弯」

再举一个例子，SAP 企业级应用 Jam 包含一些更复杂的事件。

- 公司发布了公司公告。
- 小组管理员发表了群公告。
- 同事创建/更新/删除/评论了文档。

Feed 中展示的内容是非常多样的，在后台架构的时候，如何为这种产品设计建模？

## 方案一：为每一种情况建一张表，最后通过 Union 查询聚合在一起？

创建表

- create_share_feeds
- create_video_feeds
- create_status_feeds
- create_image_feeds
- create_company_anouncement_feeds
- create_comment_feeds

然后用 Union 查询聚合产生 Feed，按时间逆序排序。

select * from create_share_feeds
union
select * from create_video_feeds
union
select * from create_status_feeds
....
order by created_at desc limit 100

这样做的缺点很明显

1. 每添加一种 Feed 类型，就必须创建一张表。
2. Union 查询无法使用索引，在数据量巨大的情况下，排序会花费很长时间。


## 方案二： 设计一张 「上帝表」，创建一个「God Class」来囊括各种情况。每当产生一个内容，就在这张表里插入一条记录？

```
CREATE TABLE feeds
(
id int,
table_name varchar(255),
table_id int
)
```

当有人产生一条「分享视频」的 Feed 时，`INSERT INTO feeds('table_name', 'table_id') VALUES ('videos', 4)`

当有人产生一条「分享图片」的 Feed 时，`INSERT INTO feeds('table_name', 'table_id') VALUES ('images', 5)`

这样做的缺点：

在企业软件中，一些非常重要的删除操作也会产生 Feed，推送给相关人员。比如「吕小荣删除了视频 xxxx」。以上这种实现方式无法囊括「更新」「删除」产生的 Feed。


## Feed 建模可能要考虑的因素

1. 被抽象的内容非常多态，未来要容易扩展。
2. Feed 不是对资源的当前状态进行抽象，它要抽象的是状态扭转(Transition)，即描述资源从A状态转化为B状态的事件。

Martin Fowler 在 [Audit Log](https://martinfowler.com/eaaDev/AuditLog.html) 一文举例，当用户更新手机号时，他不仅关心「用户的新手机号是什么」(current state)，还关心「用户的手机号从A更新为B」，他对后者做了抽象，称之为审计日志(Audit Log)。

对于一个完全不关心当前状态，而只关心状态变换的系统，他还提出了 [Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) 的设计模式。


## 一种基于事件的建模方案

1. 资源的当前状态保存在各自的表中。
2. 当状态变化时，把这一事件也保存下来，称之为 FeedEvent。
		- CreateVideoFeedEvent 创建视频
		- DeleteVideoFeedEvent 删除视频
		- CreateWikiFeedEvent  创建文档
		- UpdateWikiFeedEvent  更新文档
		- DeleteWikiFeedEvent  删除文档
		- CreateCompanyAnnouncementFeedEvent 创建公司公告
3. 每个 Event 该如何渲染，放到 Concrete class 中去实现。


![](/media/files/2018/2018-09-25-feed.png)






