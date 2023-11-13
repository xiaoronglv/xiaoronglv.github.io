---
title: Dxy 4 - 打造中文版 PubMed 数据库
layout: post
guid: M0PB4HNecWu31
tags:
  - 
---

在丁香园我把一半心血花费在一个阳春白雪的学术产品 [PubMed.cn](http://pubmed.cn/) 上。

## 前世今生

据公司的老员工讲，PubMed.cn 曾是公司的明星产品，大家寄予厚望。可倾注了无数个日日夜夜，它依然算不上合格产品：数据比官方网站陈旧，更新靠抓取；用户搜索结果不满意，可用性差。

公司不是科研机构，以商业利益为先，两年的持续投入达不到预期，就逐渐缩减投入，于是 PubMed.cn 陷入长期搁置。我跌跌撞撞的成为了 PubMed.cn 的产品经理后，没有太多顾虑。做好了，加薪升职；做不好，情有可原。

## PubMed 是什么？

美国国立生物技术信息中心旗下的 [PubMed](https://www.ncbi.nlm.nih.gov/pubmed/) 是医学/生物学科研工作者获取第一手研究资料的重要数据库，
每个月有2300万的UV，5800万查询请求[1]，它估计是最热的生物医学摘要数据库。

## 为什么要克隆一份中文版 PubMed？

当初为什么做这个产品已无从考究，我猜大概有这么几点：

0. 战略布局的一部分。通过免费产品粘住高端医生，然后在丁香通上转化为利润。
1. 丁香园以检索起家，对各个数据库十分熟悉，打造一款医学数据库是一种必然；
2. 医生晋升需要发表论文，科研需求是刚需。通过 PubMed.cn 可以牢牢的掌控住金字塔顶端的医生；
3. 拜伟大的功夫墙所赐，PubMed 官方网站十分不稳定，大陆工作者在检索文献时多有不便，急切需要稳定的服务。

## 如何克隆一份 PubMed 中文镜像网站？

**1. 获取期刊数据**

可行的数据源

- 通过 FTP 下载 [NLM catalog](https://www.ncbi.nlm.nih.gov/nlmcatalog)
- 通过爬虫下载汤森路透 JCR 的数据，并过滤其中的医学期刊。

**2. 获取期刊的影响因子**

影响因子是汤森路透的 [JCR (JOURNAL CITATION REPORTS)](https://jcr.incites.thomsonreuters.com) 数据库的一项数据，JCR 是商业产品。

在自己的网站上私自公布影响因子是违反版权的行为，会被对方诉讼。此外爬别人数据是不道德的，通过合作共赢才是长久之计。

**3. 获取 PubMed 的文献摘要数据**

PubMed 隶属于 NIH (National Institutes of Health)，所有的开发者都可以申请 [licence](https://www.nlm.nih.gov/databases/download/PubMed_medline.html) ，然后通过 FTP 下载过去几十年医学生物学文献。

不过通过FTP下载的数据并不全面，是已经归档的 Medline 数据库，PubMed 的子集。最新的文献可能处于  in-process 状态，只能通过 API 来获取。日后会更新到 Medline。

![](/media/files/2018/2018-04-17-advanced-medline-for-health-researchers-4-638.jpg)

**4. 获取文献全文**

PubMed 是摘要数据库，为什么会有全文呢？

对于 Open Access 的文献，NIH 单独为他们建立了 [PMC 数据库](https://www.ncbi.nlm.nih.gov/pmc/)，任何人都可以免费下载 PDF 全文。对于受版权保护的论文，我们只能获得右侧的全文链接，但不能获得全文。私自提供文献全文，会被各大出版社诉讼的。著名的计算机天才 [Aaron Swartz](https://www.zhihu.com/question/20710277) 曾经因盗取480万篇论文上传分享，身陷囹圄，最后自杀。

## 做二次开发的几个难点

对 PubMed 进行二次开发，首先要学习文献检索的基础知识，搞明白几个基本问题:

![Pubmed，Medline，PMC关系图](/media/files/2018/2018-02-23-pubmed-vs-medline-vs-pmc.png)

- PubMed, Medline, PMC 之间的关系。
- JCR 是什么?
- 什么是 SCI?
- 什么是影响因子？影响因子如何计算？影响因子的意义？
- 了结 PubMed 文献 XML 的结构，以及 [XML 各种属性](https://www.nlm.nih.gov/bsd/mms/medlineelements.html) 代表什么意思？
- 认认真真读 PubMed 开发者文档 [Entrez](http://www.ncbi.nlm.nih.gov/books/NBK3837/)，不要使用愚蠢的方式调用 API，增加双方的机器开销。 API 访问过频，会被封 IP。

## 最感谢的人

两年前的我真是能力不足，开发文档逻辑不通，拎不清任务的优先级。如果能穿越时空，我一定把他骂个狗血淋头。

可印华稳一直容忍我的种种缺点，给我建议，帮我成长。温柔和忍耐，是小印给我的重要一课。在以后日子里，我也有了下属。当他们做事情能力不足，我都克制自己的情绪，耐心辅导他们。

## 我们的小成果

PubMed 的搜索业务没有做好，但在印华稳的努力下，衍生的业务有了很大的起色。

1. [国家自然科学基金（NSFC）](http://nsfc.PubMed.cn)

	我们将国家自然科学基金从"丁香园文献求助平台"剥离出来，打造为独立产品。提供查询服务，以及数据可视化服务。目前它已经成为了查询 NSFC 最方便的平台。
	
2. [文献求助（Paper）](http://paper.PubMed.cn)

	文献求助平台终究是个工具。目标就是让用户更快，更方便的获得文献。所以我们做了各种浏览器插件，用户一个点击就可获得全文。
	
	通过不断的迭代和优化，求助量节节攀升，当我离开丁香园时已经达到了1万篇/周。

3. [SCI 期刊数据库](http://journal.PubMed.cn)

	很多新手第一次写SCI论文时，有种种顾虑。影响因子会不会跌？ 审稿快不快？对中国人有没有偏见？
	
	瞄准了这部分目标用户，我们推出了SCI期刊数据库。
	
4. 其他商业机密，不便引述

## 几个激动人心，但还未付诸实践的想法

- 使用 Graph Database 对作者的人脉进行分析。
- 使用 ElasticSearch 的强大聚合功能，对搜索结果进行多个维度的聚合/过滤。
- 基于论文的社交网络。
- PubMed 数据量很大，可以玩一玩数据挖掘和大数据分析。

## 收获的教训

1. 我很喜欢 PubMed，但是我不适合做它的产品经理。

	PubMed 是生物信息学的一个重要数据库，里面有几十年的技术积累。如果要打造一个中文版，需要产品负责人在医学信息检索和计算机科学两方面有很深的造诣。Python/XML/Database/ElasticSearch 是必要知识点。

	一个在生物医学信息学领域深耕3-5年的程序员，可能是最合适的人选。


2. 技术主导型的产品需要技术人员推进

	如果不懂搜索引擎，是无法改进搜索的准确性的；

	如果不懂搜索引擎/数据库，是做不好PubMed的。
	
	PubMed是技术主导型，一堆的问题都在技术层面，只懂行业经验(Domain Experience)却没有技术背景的产品经理，除了在医学会议上描绘用户故事，没有太多用处。
	

3. 多从自身找问题

	很多事情没有做好，我们经常会将责任推到别人身上，其是是逃避自己的问题。在开发PubMed的过程中，我经常抢不到前端资源，向领导吐槽成为家常便饭。现在反思当初被程序员冷落的原因，首要原因是作为产品负责人，我都不清楚产品的愿景(vision)是什么；其次文档写的不清楚；开发毫无节奏，没有 milestone，团队长期处于紧张压抑的状态。
	
	后来克服了这些缺点后，程序员都喜欢和我合作了。

	
4. 数据驱动的开发模式

	在邱岳来之前，产品的迭代都是毫无节奏的。很多功能都是一拍脑袋就开始做，做完后不知道效果如何。邱岳接手Web组后，开始要求我们从数据驱动开发。用数据说话，是我从他身上学到的宝贵经验。

	BreakDown => 设定关键指标 => 开发功能 => 发布功能 => 验证
	
	详情可参考这篇老文：[KPI是个好东西](/kpi.html)
	
5. 应该努力思考远大的产品愿景和正当的商业模式，不要在一些奇技淫巧上花费太多精力。不道德(甚至违法)的商业模式做不大，也走不远。

	影响因子功能是刚需，用户也很喜欢，但是盗用了别人的商业产品。爬数据/非法使用别人的劳动成果短期内可以让自己的产品"成功"，小赚一笔钱/[UV/PV](https://www.zhihu.com/question/20448467)。长久来看，是浪费时间，也牺牲了信誉。

	这种例子不胜枚举，比如早期迅雷上盗版猖獗，VeryCD 电驴下载，盗版 Kindle 电子书分享网站，快播通过成人视频导流。

	人生有限，不如专注在有价值的事情上，做正确的事。在影响因子这件事上，我完全可以用论文作者的 H 指数，以及论文引用次数来甄选论文，不必非要用汤森路透的影响因子。

## Notes

1. [http://www.ncbi.nlm.nih.gov/PubMed/20157491/](Understanding PubMed user search behavior through log analysis)

2. [Open Access](https://en.wikipedia.org/wiki/Open_access)

3. [PMC](https://www.ncbi.nlm.nih.gov/pmc/), PubMed Central® (PMC) is a free full-text archive of biomedical and life sciences journal literature at the U.S. National Institutes of Health's National Library of Medicine (NIH/NLM).
	
4.  [快播涉黄案](https://zh.wikipedia.org/wiki/%E5%BF%AB%E6%92%AD%E6%B6%89%E9%BB%84%E6%A1%88)
