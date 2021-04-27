---
title: 如何备考 AWS Certified Solution Architect Associate(英文)? 
layout: post
guid: b7PldUZ(C8T43
date: 2021-04-08 08:34:08
coffee:
tags:
  -
---

![](https://mednoter.com/media/files/2021/2021-04-17_18-40-27.jpg)


## 为什么备考？

我以前在 SAP 工作的时候，基础设施并没有上 AWS / GCP / Azure 共有云。

- 负载均衡是 F5 load balancer -> HAProxy -> Nginx
- 数据库的备份都是专人负责。
- 文件存储都是自己 mount volume，满了还要扩容。
- HTTPs 证书要自己申请，然后配置到负载均衡上。

运维的脏活累活都是自己干。

后来团队招聘了一个 AWS Solution Architect，天天在办公室大谈公有云的好处。RDS 的服务多么稳定，升级如何贴心，S3 存储无限空间。

文人相轻，程序员也一样。我心里常常嘀咕，你懂 MemechedCache 基于客户端的分布式吗？你懂一致性 hash 吗？你懂数据库的 Binlog 吗？你懂 Certificate Chain 吗？你懂 HTTP/2 吗？

你其实就是个 AWS 配置工程师，随便点点按钮，内部原理压根不懂。

后来我离开了 SAP，跳出了私有云的大火坑。在新的职场开始使用 AWS 全家桶。不用不知道，一用真是好，AWS 做的云产品甩出 SAP 的私有云十条街。

- 数据库可以开启 Multi-AZ，数据中心故障时，可以自动切换到另外一个 Availability Zone
- 文件可以往 S3 里随便扔，容量无限制，再也不用 mount volume
- HTTPs 的证书可以自动更新，不需要手工操作。

新世界的大门向我开启，原来运维的工作可以这么简单。我为当初的傲慢无理非常羞愧(虽然他不知道)，向那位 AWS Solution Architect 道歉。

我也冒出了考 AWS Solution Architect 的想法。

一，符合我的职业规划。国内的工作机会 996 严重，工作的自由度低。为了平衡家庭和工作，我大概率会在外企这条路继续走下去。2020年 AWS 全球市场份额占 31%，Azure 占 20%。AWS 知识可以在未来的职场不断被使用。

二，我目前所在的公司 workstream.us 重度使用 AWS 的各种产品，备考 AWS Certified Solution Architect 可以帮助我改进公司的现有的架构。备考试题中有大量的模拟场景，我可以学到应对各种场景的最佳实践。

三，AWS Certified Solution Architect – Associate 含金量很高认证，长期处于薪酬最高的程序员[排行榜前2位](https://www.globalknowledge.com/us-en/resources/resource-library/articles/top-paying-certifications/#gref)。有人统计过，持有该证书的工程师平均年薪是 11-14 万美金。如果你在外企工作，看在钱的份上，你也应该考此证书。

## 什么时候开始备考比较合适？

AWS 的产品线非常的广，如果你们公司技术栈没有真实的使用过一些产品，死记硬背效果很差。AWS 推荐程序员使用 1年 AWS 的产品之后再备考，效果更好。

学以致用。如果你们公司用到的技术栈是阿里云，不如考阿里云认证。如果你们公司用到了 Azure，不如考 Azure 的认证。这样更加事半功倍。


## 备考材料有哪些？

我的英文水平：读文档流畅，听力也还凑合，但是说话不流畅。

一开始我直接购买了 AWS 官方推荐的教材 [《AWS Certified Solutions Architect Official Study Guide: Associate Exam (Aws Certified Solutions Architect Official: Associate Exam)》](https://www.amazon.com/Certified-Solutions-Architect-Official-Study/dp/1119138558)，通过学习教材备考。

通读全书之后很快发现了几个问题：

- 不清楚考试的重点章节。
- 不熟悉考试题型。

后来翻看了国外网友的一些考试经验，发现很多人都会买在线课程，通过课程学习 AWS 知识，然后手把手的去操作一遍加深印象。然后刷几套模拟题，评估自己的真实水平。

于是我在 Udemy 上购买了销量最好的一套课程和一套模拟题：

**1.视频教学课程，作者  Stéphane Maarek**

[《Ultimate AWS Certified Solutions Architect Associate》](https://www.udemy.com/share/102CPBAEMadldSRn8J/)


**2. 模拟题，作者 Tutorials Dojo**

[《https://www.udemy.com/course/aws-certified-solutions-architect-associate-amazon-practice-exams-saa-c02》](https://www.udemy.com/course/aws-certified-solutions-architect-associate-amazon-practice-exams-saa-c02/)

tips: 我刷模拟题得分92-96分，去考场真实考试只有78分。如果你模拟题分数很高，不要对自己过分自信。

## 学习时遇到的一些困难

**学习内容中包含了大量的“用不上的知识”，只能死记硬背。**

我所在的公司是直接用的云产品，不存在数据迁移的问题。但是 AWS 考试题会有大量 on premise 数据迁移相关的内容，考察开发者帮助传统的企业上云面临的各种挑战。

把传统的数据中心的文件迁移到云上的业务，要用到 DataSync 去把本地数据中心的数据同步到 S3.

把机房的数据库同步到 AWS RDS，要用到 AWS Database Migration Service。

所以只能死记硬背这些知识。

**不得不在 AWS 一些非主流产品上上投入时间**

AWS 的大部分云产品是非常优秀的，但是还是会有很多非主流产品。

CodeCommit 是 AWS 开发的简单 git server，可以帮助开发者托管代码。现如今大家都会用 Github/Gitlab, 谁会用 CodeCommit 呢？


## 备考时间

我知道一些很聪明的程序员只备考了2个月就考试通过了。我很慢，花了一年半的时间学习备考。

首先是因为工作忙碌，每天只能用碎片时间学习一个知识点。

其次，AWS 的考试题让我看到自身薄弱的计算机基础知识，我经常会花1-2周去搞明白。说来惭愧，我工作8年了，一直没有搞明白 IP / Network Mask / CIDR。我花了2周时间彻底搞明白了。

最后，学习本身很快乐，考试通过是副产品。所以备考也不慌不忙。

这是我2020年每周在 AWS 方面的[学习的记录](https://mednoter.com/plan-for-2020.html#3%E8%80%83%E4%B8%AA-aws-certified-solutions-architect-%E8%AF%81%E4%B9%A6)，别人是日拱一卒，我是”周“拱一卒。

![](https://mednoter.com/media/files/2021/2021-04-27_11-46-41.jpeg)

## 如何预约考试？

当你在 Udemy 上模拟题可以刷到 92分左右的时候，就可以报名考试了。

预约网站： [aws.training](https://www.aws.training/)

考试费用：150 美金。

![](https://mednoter.com/media/files/2021/2021-04-27-schedule-exam.jpeg)

AWS 的考试安排会有两个渠道 “通过 PSI 安排” 和 “通过  Pearson Vue”安排。 我一开始不太懂他们是什么，区别是什么。

大千世界，无奇不有，有公司靠监考赚钱。PSI 和 Pearson Vue 是两个考试机构，你可以理解他们是专门监考的公司，AWS 和他们合作，你去 PSI 或 Pearson 公司去考试，他们会负责核对身份，监考。

两家考试机构，并无太大区别，你选择一家离你近的考点就可以。

**预约中文版还是英文版?**

如果预约中文版，考试时每个题目既可以选择中文，也可以选择英文。我的朋友预约的是中文版，他考一个半小时就交卷了。

我预约的是英文版，考试时发现和模拟题的表述差距很大，有些题目描述的非常晦涩。我每道题目要读好几遍，足足考了130分钟。


## 考试过程中遇到的一些困难

**一些题目会模拟出一个场景，然后让你选择合适的解决方案。但它提供的正确解决方案并不是完美答案。我只能用排除法排除掉明显的错误选项，勉为其难的选一个将就的答案。**

> 一个电商网站 example.com 有静态内容，也有动态内容，它面临着越来越多的流量，以下选项那些可以改进性能  
> 
> A: enable CloudFront (正确答案)  
> 
> B: Use S3 static website
> 
> C: ...
> 
> D: ...

在真实的场景里大家肯定会把静态资源剥离到单独的域名 `files.example.com` 或 `img.example.com` ，用CDN响应；动态内容则用主站渲染。

很少有人会直接给主站开启 CloudFront，然后根据不同的 url 设置不同的 CDN behavior。但是鉴于其他选项明显错误，我只能勉为其难的选 A。

**有些题目则完全是抠字眼。**

比如某些题目啰哩啰嗦的描述了一个场景，这个场景最适合的解决方案并不是 DynamoDB。但是题目中出现了一个关键字 `microseconds`, 这时候我只能闭着眼睛选 `DynamoDB + DAX` 。AWS 要昭告全世界，microseconds 太快了，快死了。如果我不选这个选项，我就要丢分了。

真希望出题的人质量高一点，能把真实世界的架构带到考试题中，让考试的人和出题的人的脑电波在一个频率上。


## 总结

AWS Certified Solution Architect Associate Exam 考察的知识点非常的全面。

如果有些知识点和你的工作密切相关，建议沉下心来吃透。

如果有些知识点一辈子你也不会用到，建议点到为止，快速带过。将来如果用到了，再深入研究即可。

祝你考试顺利。


