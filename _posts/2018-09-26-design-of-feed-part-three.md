---
title: "Feed 流设计(三)：分发逻辑"
layout: post
guid: r9x0ityxXBonrkQ19DW
date: 2018-09-26 08:34:16
tags:
  - Programming
---

Feed 后台设计文章索引

- [Feed 流设计(一)：如何对多态内容进行抽象？](http://mednoter.com/design-of-feed-part-one.html)
- [Feed 流设计(二)：拉模式 Vs 推模式](http://mednoter.com/design-of-feed-part-two.html)
- [Feed 流设计(三)：分发逻辑](http://mednoter.com/design-of-feed-part-three.html)
- [Feed 流设计(四)：存储](http://mednoter.com/design-of-feed-part-four.html)

在上一篇文章中，我们谈到推模式的种种好处，尤其是大大提升了读的性能。在推送新的动态至各个队列时，有大量的逻辑判断。以 facebook 举例，用户发布动态时，可以选择6个隐私级别。

1. 公开(Public) ，所有人都可以看到。
2. 朋友(Friends)，只有朋友才可以看到发布的内容。
3. 所有的朋友都可以看，除了某某某。(Friends except...)
4. 只给几个指定的朋友看。 (Specific friends)
5. 不允许任何人(Only Me)。
6. 只给某个组别的人看(Group)

![](/media/files/2018/2018-09-26-privacy.jpg)

我们可以用 Policy Pattern 来抽象。在《Design Pattern》一书中，GoF 将 Strategy Pattern 和 Policy Pattern 归为一类。谈论隐私时，我们习惯称之为「Privacy Policy」。为了提高代码的可读性，选用 Policy 这个术语，而不是 Strategy。

以下伪代码是对 Policy 的抽象，你可以根据自己的业务来修改数据结构。

```
class FeedPrivacyPolicy {
    string level  // e.g. public, friends, onlyme, group, list
    array allowed_user_ids
    array blocked_user_ids
    array group_ids
    array company_ids
    array tag_ids
    array user_list_ids
    
    FeedPrivacyPolicy(level, allowed_user_ids, blocked_user_ids, group_ids, company_ids, tag_ids) {
      level = level;
      allowed_user_ids = allowed_user_ids;
      blocked_user_ids = blocked_user_ids;
      group_ids = group_ids;
      company_ids = company_ids;
      tag_ids = tag_ids
    }
}
```


## 各种 Policy 范例

如果用户选择 `公开(Public) ，所有人都可以看到`，相应的 Policy 对象应该如此构建:

```
policy = new FeedPrivacyPolicy
policy.level = 'public'
```

如果用户选择 `朋友(Friends)，只有朋友才可以看到发布的内容`，相应的 Policy 对象应该如此构建:

```
policy = new FeedPrivacyPolicy
policy.level = 'friends'
```

如果用户选择 `所有的朋友都可以看，除了某某某。(Friends except...)`，相应的 Policy 对象应该如此构建:

```
policy = new FeedPrivacyPolicy
policy.level = 'friends'
policy.blocked_user_ids = [1, 33, 351]
```

如果用户选择 `只有某几个特定的朋友可以看到`，相应的 Policy 对象应该如此构建:

```
policy = new FeedPrivacyPolicy
policy.level = 'friends'
policy.blocked_user_ids = [1, 33, 351]
```

如果用户选择 `不允许任何人(Only Me)`，相应的 Policy 对象应该如此构建:

```
policy = new FeedPrivacyPolicy
policy.level = 'onlyme'
```

如果用户选择分享内容给 `一组朋友`，相应的 Policy 对象应该如此构建:

```
policy = new FeedPrivacyPolicy
policy.level = 'list'
user_list_ids = [81643]
```

![](/media/files/2018/2018-09-26-different-policies.png)

你可以根基自己的业务需要，随意的拓展各种各样的 policy。你可以使用继承的关系，让不同的 policy 更加严肃。也可以只定义一个类，然后把不同的规则简单保存为 JSON，更加灵活。


## Policy 如何与其他组建协作？

1 当用户分享内容时 (event)，会选择隐私级别 (privacy)，他们之间如何协作？

1.1 隐私级别可以作为一个属性保存到 event 中

```
policy = new Policy
event = event Event
event.policy = policy
```

1.2 也可以直接将 event 与 policy 传递给分发系统

```
class RouteService
  event event
  policy policy
  RouteService(event, policy){
    event = event
    policy = policy
  }
  
  // route! 方法是分发逻辑。
  void route!(){
  }
end
```


2 配置 context 对象 `RouteService`。

![](/media/files/2018/2018-09-26-collaborate.png)

3 route! (分发)

为了性能和用户体验，分发工作通常不会在 web server 上执行，而是委托给另外的进程去执行。

![](/media/files/2018/2018-09-26-route-service.png)

下一篇文章，我们聊 feed 的存储：Redis，MySQL，Hybrid?