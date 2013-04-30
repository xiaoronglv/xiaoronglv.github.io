---
title: PubMed Central OAI service
layout: post
guid: uFhXLl1hcPRG023119
tags:
  - 
---

## 知识背景

1. [什么是OAI?](http://www.mednoter.com/abandoned-oai.html)
2. [OAI-PMH(OAI收割协议)](http://d.wanfangdata.com.cn/Periodical_qbkx200503022.aspx)
3. [Open Archives Initiative 官方网站](http://www.openarchives.org)


## Open Archives Service

PubMed Central(PMC)是OAI的元数据提供者,全部文献的元数据,部分全文通过PMC-OAI协议提供给使用者.PMC-OAI只支持OAI-PMH2.0,不支持较早的版本.

## 文献的版权

绝大部分存档版权贵作者或杂志所有,并受到版权保护.请不要使用OAI来检索PMC的全文.只有隶属于开放存取(Open Access,OA)的杂志才才可以获取全文.

## 如何使用PMC-OAI?

URL接口: <http://www.pubmedcentral.nih.gov/oai/oai.cgi>

## 检索负荷

PMC-OAI高峰时段:周一至周五 5:00-21:00 U.S Eastern time

如果您应用脚本检索次数超过100次,请避开网络繁忙的高峰时间.即使在非高峰时间,检索频率不要超过3次/秒.PMC-OAI支持gzip方式的HTTP压缩,详情请参考[维基百科词条](http://en.wikipedia.org/wiki/HTTP_compression).

## 获取全文

一些OA的全文允许收割(harvest)全文,有些则不支持.详情请参考PMC的OA声明.使用set=pmc-open参数可以下载愿意被收割的全文.



## 提供的数据格式

1. NLM Journal Archiving and Interchange XML format
2. 都柏林核心元数据(The Dublin Core format)


## 数据分割

当使用ListIdentifiers请求的数据超过500条时,PMC-OAI返回前500条数据,使用Resumption Token可以获取剩余的数据.

当使用ListRecords请求的全文数据超过25条,或者PMC格式元数据超过50条,或者都柏林格式的元数据超过25条,PMCI-OA会返回前25条,以及一个Resumption Token.

## 联系PMC

如有任何疑问,请发送邮件给oai@ncbi.nlm.nih.gov

本文译自[PubMed Central 官方文档](http://www.ncbi.nlm.nih.gov/pmc/tools/oai/)

