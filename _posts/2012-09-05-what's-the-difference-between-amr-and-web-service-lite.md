---
title: AMR与Web Service Lite的区别
layout: post
guid: uFhXLl1hcPRG07122
tags:
  - 
---

目前WOS（Web of Knowledge）有两个API提供：AMR 与 Web Service。相信初次接触的同学都会对两者的区别十分不解，这两周一直在研究，略有心得。分享给大家。

## AMR

AMR（Article Match Retrieval）是无法实现大规模的检索的，只能根据文献记录的id，获取对应的信息，包括各种记录标识号（例如DOI/PMID/UT）和连接到Web of Science 的数据库链接。或许我们可以亲切的称它为“逐篇获取”

<span class="image-1200">![](/media/files/2012/sep/5-1.png)</span>

## Web Service lite

给定检索条件，实现大规模的搜索，获取一批符合条件的ids  
然后对这些id进行后续的操作，比如获取书目信息（文章的标题、作者、杂志名等等）

<span class="image-1200">![](/media/files/2012/sep/5-2.png)</span>

附件为xmind思维导图

1. AMR  
2. [Web Service](https://skydrive.live.com/#cid=51A78DB6D0C754DF&id=51A78DB6D0C754DF%211033)

> Give a big thanks to Ann’s support, who is working in thomsonreuters.