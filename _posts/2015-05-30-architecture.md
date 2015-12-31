---
title: 模块化的学习架构
layout: post
guid: 4lvcpham7x91
date: 2015-05-30 22:57:10
tags:
  -
---

> 我全职做 Ruby 程序员不到2年，资历浅薄，以下内容都是基于我有限的经验，如有纰漏，欢迎指正。

经常会在论坛里看到这种类型的讨论

问：我该如何设计 XXX？
答：Just make it work, then refactor it.

问：我以后如何满足高并发的需求？
答：项目赚钱是关键，量大后再砸钱。

这些回答都来自技术创业者深刻教训，自有中肯的道理。

不要过度设计。互联网产品一周一个迭代，纯 Web 产品甚至每天都在迭代。迭代的依据就是用户的反馈。如果用户对这个 Idea 毫无感觉，就立刻丢弃；如果引起巨大反响，就投入更多的资源。产品的未来是未知的，在每一次尝试中追求完美架构纯属浪费时间，而创业最缺的就是时间。

没有银弹[1]。没有任何一项技术或方法可以能让软件工程的生产力在十年内提高十倍。现实世界是复杂的，其抽象到软件层面后概念之间亦复杂。一劳永逸的架构是不存在，所有的架构都是在随着业务的复杂性在逐步蜕化。


## 逃避学习架构

未来是不可预测的，这是架构的客观困难，与此同时也成为懒惰程序员逃避架构的借口。

* 先凑合着实现这个功能吧，领导催的紧，他才不关心我如何实现呢。
* 堆完这些砖，我就辞职吧，再也不碰这些狗屎了，我要重写一个干净的项目。
* 先凑合着上去再说吧，还不知道有几个人用呢？万一火了，等我赚了大钱之后，砸钱请 BAT 的大牛来重构代码。

每个人不但不关心架构，还在逐渐侵蚀架构。日复一日的恶性循环，最终整个开发工作沦为代码的堆砌，毫无乐趣可言，每个人都成为受害者。

或许哪天运气好，一个德才兼备的程序员踏着七彩云从天而降，开始大刀阔斧的重构。结果发现各种耦合，牵一发而动全身，为了解耦要学习全部的业务以及变迁史。这简直就是 Mission Impossible，继而望而生畏，也道德沦丧的堆砌代码。

## 玄之又玄的架构能力

如果不逃避未来的问题，产品之初又该如何规划架构呢？

年长的程序员又语重心长的说：架构是敏捷与过度设计之间的平衡。

如何学习架构能力呢？

年长的程序员：它像中医玄之又玄，它只可意会不可言传。[2]

##  架构模块

> “天啊，那女孩真叫一个绝！ ”16岁的大卫 ·拉莫斯指着蒂娜 ·陈说。这位中国学生最近在草山夜间音乐会上演奏了一曲科恩戈尔德小提琴协奏曲。拉莫斯的声音突然放低，变成了窃窃私语。“她说她学了三个星期——但别人告诉我，她实际上只练了两个星期。
>
> 类似的战绩在草山司空见惯，部分原因是教师采取了**分解到极致的训练方法**。学生们把每小节的乐谱剪成长条状，塞进信封里，然后随机抽出。接着他们把那些纸条上的乐谱通过改变节奏拆解成更小的片段。
>
>“放慢练习在草山，刺耳的爆音会被拉长为鲸鱼的叫声。”

[The Talent Code](http://book.douban.com/subject/3647676/) 中介绍了一所学校 —— 草山音乐学校 (Meadowmount School of Music)，这所学校的惊人之处是：学生的训练速度是平均速度的五倍。他们在教学时强调分拆组块到了极其夸张的地步。第一步，技能学习者整体了解一项技能。第二步，尽可能的分拆成最基本的组块。第三部，花时间练习，体会，再练习。

我觉得也可以通过这种方式训练自己的架构能力。先把架构能力拆分成一个个的模块，然后逐个打磨训练。

举个例子，如果要做一个垂直电商，未来的需求有短信通知、物流跟踪、搜索、订单拆单、统计系统、赠品、Mobile App。产品之处，该如何训练自己的架构呢？

### 1. 把逻辑拆分到 Gem

调用易美、短信包、云通讯等第三方短信服务的逻辑可以抽象到一个 API Wrapper Gem 中[3]。

调用快递100获取物流信息的逻辑也抽象到一个 API Wrapper Gem 中。


### 2. 把逻辑委托给单独的进程

把搜索委托给 ElasticSearch，通过 HTTP 通讯。

把 Apple Push Notification Service 委托给 Rpush，通过 MySQL 中转数据间接通讯。

把计数器委托给 Redis，通过 Socket 通讯。

把缓存委托给 Memcached，通过 Socket 通讯。


### 3. 把逻辑拆分到单独的项目

订单的统计单独拆到一个项目，基于 Slave  database 中的数据再加工，比如汇总数据、分拆数据、统计报表，爱怎么折腾就怎么折腾。

User Center (或称之为 passport) 单独做成一个项目。

支付单独拆分到一个单独的项目中，做基础服务。[4]

各项目之间通过 MicroService 来通讯。

### 4. 把逻辑拆分到 Private Gem

如果使用 MicroService 划分系统，多个项目之中必定由很多通用的逻辑(重复代码)，比如权限管理。

假如新来了一个员工，要给他的账户开通4个系统的后台权限。每个系统都要添加一些管理员，是十分低效的行为。

我在薄荷工作的时候，Vincent 单独写了一个私有的 Gem admin_gateway，在所有的内部项目中引用，处理鉴权的事情十分的方便。

## 管理上的架构

![Dream](/media/files/2015-05-30-ideal.jpg)

每个项目经理都在追求人月神话，投入更多的人，产生更高的生产力[5]。

![Reality](/media/files/2015-05-30-reality.jpg)


然而现实世界却是这样，项目代码越来庞大，超出正常人的脑容量，添加人手也无法获得生产力的线性增长。

1. 新人培训时间越来越长。新人在开工之前要花更多的时间了解架构、总体业务等等。等他们对业务稍微熟悉，已经到了年末换工作的季节。

2. 耦合太多。为了不坑队友，不敢随意改动，即使改动也需要频繁确认。

3. 技术越来越保守。每一次升级牵扯到的代码量太大，都是伤筋动骨活，不如稳妥一些。随着时间的流逝，代码越来越保(chen)守(jiu)。

4. 大脑容量不够，无法加载整个代码的 context。每次方法之间跳跃都依赖 IO，严重降低开发效率。


![Small is better](/media/files/2015-05-30-small-is-better.jpg)

分拆增加了整体的复杂度，却让项目足够小，永远可理解。

因为足够小，工作时把整个项目的 context 加载到脑海中也不会感到吃力，
编程更有效率。

因为足够小，可以在子项目中大刀阔斧引入技术，Redis、MongoDB、Puma。只要接口不发生变化，就不会危害到整体的架构，调用者压根也不会察觉。

因为足够小，新手在短时间内就能熟悉每一行代码，并很快就能转化为生产力。

维护 Gem 的同学可以使用很多元编程的技巧，代码写的精简漂亮，再也不用担心初级程序员看不懂 EigenClass、CleanRoom、DSL。只要把文档写的通俗易懂，就不会被人吐槽。

## 最后

真的是经验太少了，很难提出高屋建瓴的观点。我也不知道所说的是否正确，权当是自己努力学习成为一个 Senior Ruby Developer 之路上的点点心得吧。

1. 拆分架构能力为几组模块，并不断打磨。

2. 为了预测的更准，要学习和产品相关的 Domain knowledge，以便对产品的未来有更好的预测。


## Reference

1. [没有银弹](http://en.wikipedia.org/wiki/No_Silver_Bullet) 是 Fred Brooks 在 1987 年所发表的一篇关于软件工程的经典论文。在欧洲中世纪的传说中，有一种叫“人狼”的妖怪，就是人面狼身。它们会讲人话，专在月圆之夜去袭击人类。而且传说中对“人狼”用一般的枪弹是不起作用的，普通子弹都伤不到也打不死它，只有一种用银子作成的特殊子弹才能把它杀死。Brooks在他最著名的随笔文章《No Silver Bullet》里引用了这个典故 ，说明在软件开发过程里是没有万能的终杀性武器的，只有各种方法综合运用，才是解决之道。

2. 中医理论为什么玄之又玄？血非血液，脾非脾脏，都是抽象的概念。二十个中医对同一个人号脉能有10种脉型，难以量化。

3. [China_SMS](https://github.com/saberma/china_sms)，已经有人造好了这个轮子

4. 陈金洲在 [架构腐化之谜](http://mp.weixin.qq.com/s?__biz=MjM5MjY3OTgwMA==&mid=204185796&idx=1&sn=bcde5da9f9c21e4069948cebbf9957f0&scene=1#rd) 一文中提及，他们把支付做成了一个基础服务，处理各个支付平台的付款，牛逼。

5. 《人月神话》 P16: 人数和时间可以互换仅仅适用于以下情况，一，任务可以分拆给参与人员；二，他们之间不需要相互的交流。估计只有农场、纺纱工人等人力密集型的工作才满足这些条件。