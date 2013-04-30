---
title: NLM Bibliographic data analysis
layout: post
guid: cuBaiwBiiMKp517
tags:
  - 
---


本文旨在分享经验给developer of NLM data.阅读此文需要的背景知识包括:

* MARC
* LocatorPlus

美国国立医学图书馆的书目信息有好几个版本的数据库给不同的开发者,比如Catfile, CatfilePlus,Serfile, LSIOU.那么他们之间的关系是什么呢? 本文通过思维导图来阐释.

## NLM Bibliographic Product

<span class="image-600">![](/media/files/2012/mar/18-1.png)</span>


**Catfile**  

Catfile= cat(catalog)+file. Catalog在图书情报学中的含义为编目.

LocatorPlus中的书目信息繁杂,只有部分书籍(或期刊)被完整的编目过.

在LocatorPlus中以MARC视图查看这些书目信息, 第995字段的值为”AUTH”,第999字段的值为”AUTH”或”CIP”,Catfile就是这类书目信息的汇总数据.

**CatfilePlus**

CatfilePlus是Catfile的加强版,收录的书目除了被NLM正式编目过的书目,还包括合作伙伴提供的专著等.

**Serfile**

Serfile= ser(serials)+file. serial在这里可不是电视连续剧的意思,在图书情报学中解释为期刊.

LocatorPlus中的期刊信息有的被NLM编目,有的未被编目,serfile照单全收,这两类信息都囊括进来.

## LocatorPlus

LocatorPlus是Web版的编目信息查看工具,可以查看NLM及其他图书馆的期刊,图书,视听材料的编目信息.

**按内容**

<span class="image-600">![](/media/files/2012/mar/18-2.png)</span>

**按数据来源**

<span class="image-600">![](/media/files/2012/mar/18-3.png)</span>

## LSIOU

<span class="image-600">![](/media/files/2012/mar/18-4.png)</span>

LSIOU(List of Serials Indexed for Online Users)包括所有曾经被Medline收录,并且用MeSH词标引过的期刊.这些期刊可能已经停刊,刊名或许已经改变,或者不再被medline收录.只要曾经被收录过,它就会在LSIOU中留下足迹.

2012版的LSIOU包括14417本杂志的信息,其中5589种目前被Medline收录,其他期刊早已物是人非,要么停刊,要么刊名已变,要么已经不被收录.

## 参考资料

1. Catfile,CatfilePlus,Serfile: http://www.nlm.nih.gov/databases/leased_tsd_marc.html
2. LSIOU: http://www.nlm.nih.gov/tsd/serials/lsiou.html
3. LocatorPlus: http://locatorplus.gov/