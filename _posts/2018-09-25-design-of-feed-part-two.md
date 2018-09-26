---
title: "Feed 流设计(二)：分发模式"
layout: post
guid: 3yP3ildZE32112Hy
date: 2018-09-25 15:35:08
coffee: y
tags:
  - Programming
---

在上一篇文章中，我们谈到「如何用事件抽象Feed中多态的内容」，这篇文章会进一步讨论事件的分发问题。

当用户分享一个视频时，该视频应该出现在他 Follower 的 Feed流中。比如吕小荣和 Sam 是 facebook 好友，吕小荣更新状态「我今天感觉好极了」。Sam 登录 facebook 时，会在自己的 feed 流中看到吕小荣的状态更新。

如何实现？通常有拉和推二种做法。

## 方式一：拉

Sam 有7个 facebook 好友，每次 Sam 登录，只需要遍历所有好友的产生的 event，按时间逆序排列即可。

step1: 查询 Sam 朋友的主键 ID

```sql
select friend_id from friendships where user_id = 3
=> [1, 4, 8, 10, 33, 78, 101]
```

step2: 在上一篇文章中，我们用 event 来抽象 feed流中的事件，并且保存在 table `event` 中。根据好友的主键 ID 过滤 event，并且渲染给 Sam 即可。

```sql
select * from events where user_id in [1, 4, 8, 10, 33, 78, 101] order by id desc
```


优点

1. 实现简单。
2. 关注/取消关注后，feed 中的内容会动态变化，易于维护。
3. 与「推模式」相比，没有冗余数据，不占用存储空间。

缺点

1. 当用户超过1000个好友时，读的效率很低。
2. 随着产品的发展，会出现很多奇奇怪怪的逻辑关系，SQL查询的效率会非常低，或者压根无法实现。
    - 假装关注。虽然我关注你了，但是我压根不想在feed中看到你/
    - 虚假的友谊。虽然我们互相关注了，但是我屏蔽了你的动态，不想在feed中看到你。

## 方式二：推。

1. 每个用户拥有自己的队列，在自己的队列中读取 feed 流信息。
2. 当关注的人产生 feed ，根据规则写到不同的队列中。写入前，检查各种规则，如果不满足条件，就不写入。

![](/media/files/2018/2018-09-25-queue.png)

比如 Ryan 在 facebook 有三个朋友 Sam，Bell，Zoey， Bell 嫌弃 Ryan 话多，偷偷屏蔽了Ryan。

当 Ryan 发布了一条动态 「Today is great!」(红颜色6)，系统应当只分发该动态至 Sam 和 Zoey 的队列。因为 Bell 屏蔽了 Ryan，所以 Ryan 的更新并不会分至 Bell 的队列。

下次 Sam/Zoey登录时，会在自己的队列中看到 Ryan 的动态更新 `6`；Bell登录时，则不会看到 `6`。


![](/media/files/2018/2018-09-25-friends.png)

优点

1. 读的性能高。
2. 可以覆盖各种各样的业务场景。

缺点

1. 需要大量的写入，尤其是当某个用户有100万的 follower 时，写入会非常耗时，需要额外的优化。
2. 当用户的关注/取消关注时，要更新队列，剔除已经失效的feed 内容。
3. 常年不登录的僵尸用户的queue，会占用大量的存储空间，如果存储层是内存型数据库，每个月的花费不菲。所以有人会对僵尸用户采取额外的处理策略。

## 推模式可以灵活应对各种业务场景

随着产品的发展，会出现各种各样的商业场景和 feed 流相关。

1. 公司拥有自己的 feed 主页。 (CompanyFeed)  
    e.g. [SAP 的facebook 主页](https://www.facebook.com/SAP/)

2. 热门的 tag 会有自己的 feed 主页，用户可以关注标签。 (TagFeed)  
    e.g. [facebook RocksDB 的 tag页面](https://www.facebook.com/search/str/%23rocksdb/keywords_search)

3. 一本书/股票/电影/工作机会/线下聚会也会有自己的 feed 主页  (ObjectFeed)

4. 一个人会有自己的 public feed 主页。(ProfileFeed)    
    e.g. [吕小荣的facebook主页](https://www.weibo.com/1876851727/profile)

5. 一个兴趣小组会有自己的 feed 主页。 (GroupFeed)

6. 一个人会有特别好友，他有时只想看特别好友的更新。 (CloseFriendFeed)

理论上讲，当 Ryan 在兴趣小组 [`RocksDB Developers Public`](https://www.facebook.com/groups/rocksdb.dev/) 更新一条带标签的动态 "#RocksDB is great!" 时，
这条内容应该出现在 ②, ④, ⑤，⑥ 四个地方：tag `RocksDB` 的feed 主页；Ryan 的个人主页；兴趣小组 RocksDB Developers Public 的主页；Ryan 密友的主页。

![](/media/files/2018/2018-09-25-rocksdb.jpg)


## 推模式下，各个组件如何协作？

推的模式可以将分发逻辑写在代码里，而不是查询语句里。然后根据逻辑将新的动态(feed_event)分发至符合条件队列里，满足各种各样的业务需求。受众只需要在相应的队列里读取数据即可，无需复杂的查询语句，大大提高了读的效率。

![](/media/files/2018/2018-09-26-route.png)


下一篇文章，我们来谈如何对分发逻辑的抽象。