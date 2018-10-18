---
title: Feed 的架构
layout: post
guid: WocDzcY4u4jdq19vH6e
date: 2018-08-28 08:54:21
coffee: y
tags:
  - Programming
---

## 什么是Feed流？

Feed 的意思「喂养」，把信息比作饲料，用户比作动物。如果它想吃，就可以源源不断的刷下去，把时间耗费在里面。Facebook/Linked 的首页，微信朋友圈，都是「Feed流」设计。

## 第一个问题：如何抽象 Feed 中多样性的资源？

Facebook 是社交领域的标杆，它的Feed流中包含哪些内容呢？我随机抽样了几种内容。

![](/media/files/2018/2018-08-28-demo.jpg)

1. Ryan 分享一本书《21世界的管理挑战》

2. Ryan 上传了一个视频「带儿子去吃烤鸭」

3. Ryan 发布了状态「我今天感觉不错」

4. Ryan 分享了一张图片「带儿子在上海交大遛弯」

企业级应用或许包含一些更复杂的事件，比如：

- 公司发布了公司公告。
- 小组管理员发表了群公告。
- 同事创建/更新/删除/评论了文档。

Feed 中展示的内容是非常多样的，我们在建模时要考虑：如何对不同种类的资源抽象？

## 第二个问题：一个产品内部通常会有几种 Feed流？如何构造这些Feed？

随便在 Facebook 点一点就会发现，它内部有不同用途的 Feed流。

1. Ryan 打开 Facebook，映入眼帘的 Feed 流包含了朋友/公司/小组产生的内容。(UserFollowFeed)

2. 当陌生人想要了解 Ryan 的性格和爱好时，会查看 Ryan 的[个人主页](https://www.facebook.com/xiaoronglv)。个人主页的Feed流包含了 Ryan 过去几年在Facebook说过的话，分享的书，拍的照片。(UserProfileFeed)

3. Facebook 上有各种兴趣小组，每个小组的主页都会以 Feed流 的形式展示产生的所有内容，按时间由近至远排序。 e.g. [Facebook Group: Fluent Forever Community](https://www.facebook.com/groups/fluentforever/) (GroupProfileFeed)

4. Facebook 上有各种公司的黄页，拿 Ryan 目前所在的 [SAP](https://www.facebook.com/SAP/) 举例，该公司的主页包含了既往发布的信息。 (OrganizationProfileFeed)


如果把每个Feed流看作一个集合，他们的内容有大量交集。假如 Ryan 在兴趣小组 Fluent Forever Community 发表了一篇文章《如何学英语》，以下三个集合中会出现这篇文章。

- Ryan 的个人主页 (UserProfileFeed)
- Fluent Forever 的个人主页 (GroupProfileFeed)
- Fluent Forever 所有小组成员的主页 (UserFollowFeed)

![](/media/files/2018/2018-08-28-set.png)

## 第三个问题：如何处理好隐私？

在发布内容时，用户可以选择隐私级别：

1. 公开(Public) ，所有人都可以看到。
2. 朋友(Friends)，只有朋友才可以看到发布的内容。
3. 所有的朋友都可以看，除了某某某。(Friends except...)
4. 只给几个指定的朋友看。 (Specific friends)
5. 不允许任何人(Only Me)。
6. 只给某个组别的人看(Group)

![](/media/files/2018/2018-08-28_11-54-23-privacy.jpg)

## 解决第一个问题：对Feed中的内容抽象。



什么是 audit_log

audit_log 的子类包含哪些事件？


## 解决第二个问题：构造 Feed流的数据集合。



## 解决第三个问题：用 [Policy Pattern](https://en.wikipedia.org/wiki/Strategy_pattern) 对隐私级别抽象


## 最终架构









