---
title: MeSH词系列之一：MeSH Browser
layout: post
guid: hjNBqXFwa6m53114
tags:
  - 
---


## MeSH Browser


<span class="image-1200">![http://www.nlm.nih.gov/mesh/MBrowser.html]({{ site.url }}/media/files/2012/mar/15.png)</span>
 

## MeSH Browser介绍

MeSH Browser是在线查看医学主题词(Medical Subject Heading, MeSH)工具. 你可以检索,浏览感兴趣的主题词的scope notes(不知何意), 注释(annotations), 入口词(entry vocabulary), 历史备注(history notes), 限定词(allowable qualifiers)等,查看该主题词与其他主题词的层级关系.这个工具只是一个快速检索MeSH词的工具, 并不是PubMed的替代产品.

MeSH Browser中的MeSH词实时更新, 每周都会追加补充概念主题词词.

无需专业词汇及语法规则就可以使用MeSH Browser检索感兴趣的主题词.比如你输入male neoplasm, MeSH Browser的显示结果如下:

Breast Neoplasms, Male (a MeSH descriptor)
Genital Neoplasms, Male (a MeSH descriptor)
Male Breast Neoplasms (an entry term to MeSH)
Male Genital Neoplasms (an entry term to MeSH)
Etc.


通过以下条件来过筛选检索结果:

* 主题词 (Main Heading)
* 限定词 (Qualifiers)
* 补充概念主题词 (Supplementary Concepts)
* 以上全部 (All of the Above)

化学物质及补充概念主题词既不是主题词,也不是限定词,可以通过以下条件筛选:

* Heading Mapped To (HM) (Supplementary List)
* Indexing Information (II) (Supplementary List)
* Pharmacological Action (PA)
* CAS Registry/EC Number (RN)
* Related CAS Registry Number (RR)

浏览单个主题词时,与之相关的各个字段都会显示出来,比如收录指南,定义等.

<span class="image-1200">![]({{ site.url }}/media/files/2012/mar/15-1.png)</span>


**概念视图(Concept View)**

查看主题词或者扩展补充概念主题词时可以选择”概念结构”(Concept Structure)来查看存储在xml中的结构样式. [点此下载](http://www.nlm.nih.gov/mesh/filelist.html)

1. 概念视图(concept view)  

	概念将同义词条归纳为一个概念.详情请参考[Concept Structure in XML MeSH](http://www.nlm.nih.gov/mesh/concept_structure.html). 概念视图可以提供简化的结构,比如词与备注.
	
2. 扩展概念视图(expanded concept view)  

	扩展视图显示更丰富的概念属性,例如:概念唯一的ID(concept UI),语义类型(Semantic Type),专家词典(Lexical).这些数据存储在xml中,MeSH Browser读取数据并呈现出来.[点击查看XML所有的元素](http://www.nlm.nih.gov/mesh/xml_data_elements.html)
