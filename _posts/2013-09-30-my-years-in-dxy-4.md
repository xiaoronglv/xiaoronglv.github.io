---
title: Dxy 4 - PubMed
layout: post
guid: M0PB4HNecWu31
tags:
  - 
---

在丁香园我有一半的时间是在做一个阳春白雪的产品 —— PubMed.cn。

## PubMed 是什么

美国国立生物技术信息中心旗下的 PubMed 是全世界最大的生物医学数据库，每个月有2300万的UV，5800万查询请求[1][]。

它是生物医学最重要的一个数据库，是科研工作者获取第一手研究资料的入口。


## 为什么要克隆一份中文版

在我来之前，这个产品已经开发了2年，当时为什么做已无从考究，我猜大概有这么几点：

1. 丁香园以检索起家，对各个数据库十分熟悉，打造一款医学数据库是一种必然；
2. 撰写论文的用户是专家级任务，是医学的意见领袖，通过 PubMed.cn 可以牢牢的掌控住这部分用户；
3. 拜伟大的功夫墙所赐，PubMed 官方网站十分不稳定，大陆工作者在检索文献时多有不便，急切需要稳定的服务。

## 如何克隆一份中文版？

**获取期刊的影响因子**

影响因子是汤森路透的 [JCR (JOURNAL CITATION REPORTS)](https://jcr.incites.thomsonreuters.com) 数据库的一项数据，是人家的独家产品。在自己的网站上私自公布影响因子是违反版权的行为，会被对方诉讼。

**拉取 PubMed 的数据**

PubMed 隶属于 NIH (National Institutes of Health)，开发者申请 [licence](https://www.nlm.nih.gov/databases/download/pubmed_medline.html) 后，可以通过 FTP 下载过去几十年 95 % 的医学生物学文献摘要数据。

为什么只能下载 95% 的数据？因为通过 FTP 下载的是 PubMed 的子集 Medline，已经整理归档的文献。最新的文献可能处于  in-process 状态，只能通过 API 来获取。日后 Medline 入库后，再覆盖掉老的记录。

**如何获得文献全文**

对于 Open Access 的文献，NIH 单独为他们建立了 [PMC 数据库](https://www.ncbi.nlm.nih.gov/pmc/)，任何人都可以免费下载文献全文。对于非 Open Access 的文献，是受版权保护的。开发者可以抓取网页右侧的全文链接，但不能获得全文。期刊编辑都是靠版权费养家糊口，私自提供文献全文也会被各大出版社诉讼的。


**对 PubMed 进行数据挖掘，有几个问题一定要先搞懂**

- 文献 XML 的结构
- [XML 中一些重要属性代表什么意思](https://www.nlm.nih.gov/bsd/mms/medlineelements.html)
- 认认真真读开发者文档 [Entrez](http://www.ncbi.nlm.nih.gov/books/NBK3837/)，不要使用愚蠢的方式调用 API，增加双方的机器开销。 API 访问过频，会被封 IP。


## 今生前世

据公司的老人讲，大家曾对 PubMed.cn 寄予厚望，它曾一度是公司的明星产品。可倾注了无数个日日夜夜，它依然是个半成品：数据更新基本靠抓，搜索结果基本靠猜。

公司以「结果」为导向，2年的持续的投入没有任何产出，PubMed.cn 处于一个十分尴尬的位置。

我跌跌撞撞的成为了 PubMed.cn 的负责人。

产品已经做的很烂了，反而让我没有太多顾虑，一心要把它做好。

没有资源就抢，甚至厚颜无耻的霸占程序员的私人时间。为获得领导的怜悯，一点小成果也要昭告全公司。

## 我最感谢的人

2年前的我真的太菜了。文档丢三落四，逻辑不顺，拎不清任务的优先级。如果能穿越时空和2年前的我合作，我一定会把他骂个狗血淋头。

可小印一直容忍我的种种缺点，给我建议，帮我成长。

在以后日子里，我也有了下属。即使他们做事情时脑袋很混，我都克制自己的情绪。

温柔和忍耐，这是小印给我的重要一课。


## 我们的小成果

在我和小印的努力下，PubMed的搜索业务虽然没有做好，但是很多衍生的产品却有了很大的起色。

1. [国家自然科学基金（NSFC）](http://nsfc.pubmed.cn)

	我们将国家自然科学基金从Paper平台剥离出来，并对数据进行了可视化。
	
	目前它已经成为了查询 NSFC 最方便的平台。
	
2. [文献求助（Paper）](http://paper.pubmed.cn)

	文献求助平台终究是个工具。目标就是让用户更快，更方便的获得文献。所以我们做了各种浏览器插件，用户一个点击就可获得全文。
	
	通过不断的迭代和优化，求助量节节攀升，当我离开丁香园时已经达到了1万篇/周。

3. [SCI期刊数据库](http://journal.pubmed.cn)

	很多新手第一次写SCI论文时，有种种顾虑。影响因子会不会跌？ 审稿快不快？对中国人有没有偏见？
	
	瞄准了这部分目标用户，我们推出了SCI期刊数据库。
	
4. ..

	其他商业机密，不便引述


## 收获了哪些教训


1. 我很喜欢 PubMed，但是我不适合做它的产品经理。

	PubMed 是生物信息学的一个重要数据库，里面的搜索算法有几十年的技术积累。如果要打造一个中文版，需要产品负责人在数据挖掘方面有很深的造诣。Python、XML、R都是必备素质。

	一个在生物信息学领域深耕3-5年的程序员，是最适合的人选。


2. 技术主导型的产品需要技术人员推进

	如果不懂搜索的实现原理，是无法改进搜索的准确性的；
	
	如果不懂贝叶斯，是无法改进anti-spam的；
	
	如果不懂数据挖掘，是做不好PubMed的。
	
	PubMed是技术主导型，一堆的问题都在技术层面，解决了技术难点就可成功。
	

3. 多从自身找问题

	很多事情没有做好，我们经常会将责任推到别人身上，其是是逃避自己的问题。
	
	在开发PubMed的过程中，我经常抢不到前端资源，向领导吐槽成为家常便饭。现在反思当初被程序员冷落的原因，无非就是文档写的不清楚，时间点安排的不好。
	
	后来克服了这些缺点后，程序员都喜欢和我合作了。

	
4. 用数据说话

	在邱岳（二爷）来之前，产品的迭代都是毫无依据的。很多功能都是一拍脑袋就开始做，做完后不知道效果如何。
	
	二爷接手Web组后，开始要求我们从数据的角度分析产品。
	
	用数据说话，是我从二爷身上学到的一个大招。
	
	详情可参考这篇老文：[KPI是个好东西](/kpi.html)
	

## Reference

1. [http://www.ncbi.nlm.nih.gov/pubmed/20157491/](Understanding PubMed user search behavior through log analysis)

2. [Open Access](https://en.wikipedia.org/wiki/Open_access)

3. [PMC](https://www.ncbi.nlm.nih.gov/pmc/), PubMed Central® (PMC) is a free full-text archive of biomedical and life sciences journal literature at the U.S. National Institutes of Health's National Library of Medicine (NIH/NLM).
	
