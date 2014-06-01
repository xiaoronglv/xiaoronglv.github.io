---
title: MeSH词之 概念
layout: post
guid: cuBaiwBiiMKp216
date: 2012-03-16 13:00:00
tags:
   -
---

![]({{ site.url }}/media/files/2012/mar/16.png)

XML格式的MeSH中,语义相近的词(Term)被归为一个概念(Concept),概念与补充概念词(Supplementary Concept Records)不同,请勿混淆.每个主题词有多个概念构成,每个概念又由多个同义词构成,例如:

Cardiomegaly [Descriptor]
     Cardiomegaly                      [Concept, Preferred]
          Cardiomegaly                    [Term, Preferred]
          Enlarged Heart                  [Term]
          Heart Enlargement               [Term]
     Cardiac Hypertrophy               [Concept, Narrower]
          Cardiac Hypertrophy             [Term, Preferred]
          Heart Hypertrophy               [Term]
心脏肥大(cardiomegaly)这个主题词由2个概念,5个同义词构成. 每个概念(concept)用最常用的同义词(Term)命名,同理每个主题词用最常用的概念命名. 由此可以得出: 主题词(descriptor)=最常用的同义词(Term)

同一概念内的同义词可能差别不大,但是隶属于不同概念之间的同义词的意义可能相去甚远.

## AIDS Dementia

拿这个复杂的主题词举例,它有3个概念,12个同义词.

AIDS Dementia Complex [Descriptor]

 1. AIDS Dementia Complex                                   [Concept, Preferred]
	* AIDS Dementia Complex                                 [Term, Preferred]
	* Acquired-Immune Deficiency Syndrome Dementia Complex  [Term]
	* AIDS-Related Dementia Complex                       [Term]
	* HIV Dementia                                          [Term]
	* Dementia Complex, Acquired Immune Deficiency Syndrome [Term]
	* Dementia Complex, AIDS-Related                        [Term]


2. HIV Encephalopathy                                       [Concept, Narrower]
	* HIV Encephalopathy                                    [Term, Preferred]
	* AIDS Encephalopathy                                   [Term]
	* Encephalopathy, HIV                                   [Term, Preferred]
	* Encephalopathy, AIDS                                  [Term]

3. HIV-1-Associated Cognitive Motor Complex                [Concept, Narrower]
	* HIV-1-Associated Cognitive Motor Complex              [Term, Preferred]
	* HIV-1 Cognitive and Motor Complex                     [Term]


## Exercise

Exercise [Descriptor]
1. Exercise                    [Concept, Preferred]
	* Exercise                  [Term, Preferred]
	* Exercise, Physical        [Term]
2. Exercise, Aerobic           [Concept, Narrower]
	* Exercise, Aerobic         [Term, Preferred]
	* Aerobic Exercise          [Term]
3. Exercise, Isometric         [Concept, Narrower]
	* Exercise, Isometric       [Term, Preferred]
	* Isometric Exercise        [Term]


## Aspirin

再举个化学物质的例子

Aspirin [Descriptor]

1. Aspirin                        [Concept, Preferred]
	* Aspirin                      [Term, Preferred]
	* Acetylsalicylic Acid         [Term]
	* 2-(Acetyloxy)benzoic Acid    [Term]

2. Solprin                        [Concept, Narrower]
	Solprin                      [Term, Preferred]

3. Ecotrin                        [Concept, Narrower]
	Ecotrin                      [Term, Preferred]

这种标准的三层结构仅用于呈现一个主题词,主题词与主题词之间的结构用[MeSH树状结构](http://www.nlm.nih.gov/mesh/intro_trees.html)表示.MeSH的XML文件中包含这两种结构.

目前MeSH Concept并未应用于Medline索引,PubMed搜索,但它已经在其他领域大展身手,比如供分析师创建更新MeSH词,与统一医学语言系统[(Unified Medical Language System,UMLS)](http://www.nlm.nih.gov/research/umls/umlsmain.html)整合,构建MeSH翻译维护系统(MeSH Translation Maintenance System).在未来的日子里,MeSH concept一定能在高级信息检索中获得更高层次的应用.

## 更多信息

1. [“Concepts, synonyms, and Descriptor structure” in Introduction to MeSH in XML format.](http://www.nlm.nih.gov/mesh/xmlmesh.html)

2. [NLM Technical Bulletin article Changes in MeSH Data Structure (includes the example “AIDS Dementia Complex”).](http://www.nlm.nih.gov/pubs/techbull/ma00/ma00_mesh.html)

3. [The poster presentation Redefining a Thesaurus (includes the example “Exercise”).](http://www.nlm.nih.gov/mesh/redefine.html)

4. [Book Chapter Relationships in Medical Subject Headings.](http://www.nlm.nih.gov/mesh/meshrels.html)

5. The section “Concept Structure of MeSH” in the article [The MeSH Translation Maintenance System](http://www.nlm.nih.gov/mesh/mtms_medinfo_2004.html) and the related slide presentation [The MeSH Translation Database](http://www.nlm.nih.gov/mesh/presentations/medinfo2004_mtms/index.htm) (includes the example “Cardiomegaly”).[](http://www.nlm.nih.gov/mesh/mtms_medinfo_2004.html)

6. Articles [Tracking Meaning Over Time in the UMLS® Metathesaurus®](http://www.nlm.nih.gov/mesh/trackingmeaning.html), and [Beyond Synonymy: Exploiting the UMLS Semantics in Mapping Vocabularies](http://www.nlm.nih.gov/mesh/beyond.html), which explicate the role of Concepts in the UMLS Metathesaurus.[]()