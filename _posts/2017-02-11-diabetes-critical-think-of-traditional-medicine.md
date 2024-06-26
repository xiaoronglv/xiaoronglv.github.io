---
title: "可习得理性: 统计思维"
layout: post
guid: qgqVHuQohcUqcC239s1
date: 2017-02-10 15:14:05
tags:
  - 
---

> 本文是2013年间开微信公众号"糖尿病饮食"时所写的文章，不忍丢弃，所以归档到博客之中。

![](/media/files/2017/2017-02-10-critical-thinking-1.jpg)


阅读这篇文章的你可能是资深程序员，成功的产品经理，出色的金融分析师，有品味设计师，或餐饮店的老板。

在你所在的领域，你很出色，掌握了行业最前沿的知识。你写的代码可能比我漂亮；你设计的产品可能日活比我高；你在金融市场获得几倍，几十倍的回报率；你设计的产品简洁，易用，容错；你做的饭更精致，好吃。

但我有个自大的观点「进入你的领域两年，我就可以达到70分水平」。看到这个自大的宣言，希望你不要弃读。因为作者自认资质平凡，只是是为了表达一个观点：每个领域都沉淀了一些拿来就用的实用知识，花费很少时间即可以掌握，性价比出奇的高。

我做过医学生，产品经理，程序员，在创业公司时还兼职了一些杂活，比如活动策划，网站运营，数据产品负责人。拿个人的经验举例可能不妥，但是还是可以说明一些共性。

刚做产品经理时，当务之急是学习 Axure ，就可以照葫芦画瓢设计原型了。然后再读《设计心理学》《启示录》等书籍，然后再学习如何分析各种流量数据。边学边练，两年时间绰绰有余。

刚做程序员时，当务之急是学习框架的API，然后疯狂的写代码，让别人严厉的审核自己的代码，阅读各种优秀的设计范式，不断应用。边学边练，两年时间做个熟练工绰绰有余。

吾生也有涯，而知也无涯，更可悲的是知识在以指数级爆炸。细分学科，为各学科设定界限，似乎是现代社会的协作方式，同时大多数人也默许了这一悲观结论掌握跨学科学科知识似乎是不可能完成的任务。

但是数点人类近年来的成就，越来越多的变革并非来自本行业的努力，而是其他行业的成就造就了自己。

核，原子，磁场在物理学的界限之内，有人把它应用到医学成像，发明出核磁共振成像，获得了诺贝尔奖。认知偏差在心理学界限之内，有人将它与心理学结合起来，推翻了传统的经济人假设。经济学家获得了诺贝尔奖，商人获得通过跨学科知识在资本市场获得了巨大的回报。[1]

学科因认识世界而设立，而世界不按学科孤立的运行。Richard Hamming 介绍了贝尔实验室的两种失败的科学家，一类科学家总是关着门，专心致志，但是科研方向可能是错的。另一类科学家总是开着门，吸纳各种好想法，很难专注，碌碌无为[2]。

我们在术业有专攻的同时，如果花费少量的时间去掌握其他领域高性价比的知识，并在自己的头脑里形成一个复杂的网络，就可以更加理性的看待这个世界，也更容易成功，是不是一举两得？

本文介绍医学科研领域的性价比极高的一把宝剑，统计学思维。装备了统计思维，你将会用全新的方式思考新闻不实报道，养生秘籍，偏方，特效药，广告商伎俩。

人的感性偏差何其大，用统计思维摈弃这些偏差又是何其简单。

## 统计学

医学统计学是数学的一个分支，非常深奥，一本教材都写不完，本文建议大家用这个简单思维模式考虑问题：

1. 选取样本
2. 区分为对照组，实验组。
3. 施加干预
4. 对比分析
5. 结论

很多临床医学研究都遵守类似流程，不遵守统计流程得出来的结论，是无法获得国际承认的，也无法发表在科学刊物上（比如Nature、Science）。

简而言之，医学统计学就是一个被医学临床科研工作者普遍认可的，研究客观规律锤头：通过「样本」来推测「全体」。

## 生活中的统计学

世界上大部分谎言是经不住统计学这把锤子敲打的。比如红酒的厂商可能说「喝红酒可以延长寿命」，他们的结论有可能是这样的出来的：观察60个喝红酒的人，有50个都活到80岁，所以得出「喝红酒可延长寿命」的结论。

常人看上去是没有问题的，但是经过统计学训练的人会这样思考。

1. 在中国，喝葡萄酒的人受教育程度更高，生活更富裕，这些人群更关心自己的饮食和健康，定期体检概率更高，他们长寿很自然。教育程度、收入、体检频率都是该考虑的因素，研究者却没有提及。

2. 要命的是，这个研究没有对照组。上海女性的平均寿命已经 84 岁，如果我拿他们做样本，可以得出任意我想要的结论。比如说上海话可以长寿 / 喝上海水可以长寿 / 吸上海空气可以长寿。

那如何证明喝红酒可以延长寿命呢？

1. 选取一定样本量的人(比如200个)，年龄、性别、受教育程度等等相近。
2. 分为两组，一组喝红酒（并且要记录喝酒的量），一组不喝红酒。
3. 20年后，观察他们寿命、死亡率等。
4. 分析数据，做统计学分析。
5. 得出结论：红酒可以（或不可以）延长寿命。

这样才能得出令人信服的结论。这种实验设计方法叫做队列研究，也可以使用其他实验设计方法，比如病理对照研究，本文就不赘述了。

## 如何质疑？

这种例子我还能举出很多。因此我建议各位，当你看到一项研究的结论时，你的第一反映就应该是质疑！

1.样本量（病人数量）是多少？

假如5个人吃了吕小荣的药方子，血糖稳定了。吕小荣就夸大其词的说「该药可以治疗糖尿病」，这种结论不可信。因为样本量（病人数量）太少。
    
2.有没有对照组？

有些医生大肆鼓吹「100个病人使用他的治疗方法后90个痊愈，治愈率90%」，这种结论也是没有意义的。
    
如果按照这种逻辑，我可以放胆的说「吕小荣使用神奇药丸治疗病毒性感冒，治愈率是100%」，可这种结论有意义吗？

稍有常识的人都知道，治好病毒性感冒并不是神奇药丸的功效，因为病毒性感冒是自愈性疾病，2周内基本自愈。

所以，我们来评价一个药有没有效果时，要问：这个药有没有做过随机双盲实验？[3]
 
我们来评价一个治疗方法有没有效果时，要问：相对于传统的治疗方法，新的治疗方式比老方式提高了百分之几？有没有统计学意义？

3.实验组（施加特殊处理的组）和对照组的病人是否有严重的偏差？

年龄是否均匀?
性别是否均匀?
教育程度是否均匀？

4.两者是相关关系，还是因果关系。

比如当有人说「喝咖啡的人更长寿」，你的第一反应应该是：喝咖啡的人受教育程度更高，家境更富余，更注重身体健康。喝咖啡可能与长寿相关，但是可能并不是因果关系。如果要证明「喝咖啡会长寿」，应该排除教育程度，收入等因素的影响。

再举一个例子，当有酸奶厂商说「莫斯利安，长寿村的秘密」时，你的第一反应应该是：长寿村的人可能是特定种群的人，他们的平均年龄本来就高。如果要说明莫斯利安酸奶的菌群确实会让人长寿，应该多调查几个也喝同类酸奶的地区，看一看他们的平均年龄。

一个真正医生科研工作者，在做科研时，要经过周密的设计，避免得出错误的结论。否则在发表论文时会遭到审稿人质疑，轻则修回补数据，重则拒稿。误导大众健康，那更是严重后果了。

## 你现在具备统计学的思维了吗？

假如有一篇论文标题为：神丹妙药AAA对肺癌的治疗应用

作者先培养了一批肝癌细胞，然后往细胞的培养液里撒了一点「神丹妙药AAA」，发现肿瘤细胞不生长了，甚至有些开始死亡。于是作者就得出结论：该药可以抗癌。

如果运用刚才的统计学知识，你会发现这个研究有什么问题？

## 参考资料

1. 理查德·泰勒的主要研究领域是行为经济学、行为金融学与决策心理学。2017年10月，因行为经济学方面的贡献获得2017年诺贝尔经济学奖。

2. [You and Your Research.](https://www.cs.virginia.edu/~robins/YouAndYourResearch.html)

3. [双盲实验](https://baike.baidu.com/item/%E5%8F%8C%E7%9B%B2%E5%AE%9E%E9%AA%8C/4880531)