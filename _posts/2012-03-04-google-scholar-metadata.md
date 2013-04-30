---
title: Google Scholar支持多种结构化数据标准
layout: post
guid: lGvnjByj8Rpc11
tags:
  - 
---

目前google已经支持结构化的数据,搜索科技文献的结果列表中可以显示各种信息,比如作者,出版日期,引用次数等.用户不需要跳转到目标网站也可以对掌握文献的基本信息,这些是如何做到的呢?本文就科技文献的SEO做一些基本知识的介绍.

以下截图是在google中检索Nature的一篇科技文献”Empowering Young Scientists“的搜索结果.有兴趣的读者可以拿四大临床杂志JAMA,NEMJ,Lancet,BMJ的其他文献做一下实验,他们的科技文献都会呈现出这种效果.如果在搜索引擎的列表页面都能呈现出这种效果,无疑会极大的提升用户的点击率.

<span class="image-600">![](/media/files/2012/mar/4-1.png)</span>



## 为什么与众不同?

因为[Nature的文章](http://www.nature.com/nrneurol/journal/v6/n2/full/nrneurol.2009.217.html)网页的\<head>下的metadata十分丰富.

	<code>
	<link title="schema(PRISM)" rel="schema.prism" href="http://prismstandard.org/namespaces/1.2/basic/" />
	<meta name="prism.copyright" content="&#169; 2010 Nature Publishing Group" />
	<meta name="prism.rightsAgent" content="permissions@nature.com" />
	<meta name="prism.publicationDate" content="2010-02-01" />
	<meta name="prism.volume" content="6" />
	<meta name="prism.number" content="2" />
	<meta name="prism.section" content="Review" />
	<meta name="prism.startingPage" content="78" />
	<meta name="prism.endingPage" content="87" />
	<meta name="prism.publicationName" content="Nature Reviews Neurology" />
	<meta name="prism.issn" content="1759-4758" />
	<meta name="prism.eIssn" content="1759-4766" />
	
	<link title="schema(DC)" rel="schema.dc" href="http://purl.org/dc/elements/1.1/" />
	<meta name="dc.publisher" content="Nature Publishing Group" />
	<meta name="dc.language" content="en" />
	<meta name="dc.rights" content="&#169; 2010 Nature Publishing Group" />
	<meta name="dc.title" content="The use of PET in Alzheimer disease" />
	<meta name="dc.creator" content="Agneta Nordberg" />
	<meta name="dc.creator" content="Juha O. Rinne" />
	<meta name="dc.creator" content="Ahmadul Kadir" />
	<meta name="dc.creator" content="Bengt L|[aring]|ngstr|[ouml]|m" />
	<meta name="dc.identifier" content="doi:10.1038/nrneurol.2009.217" />
	<meta name="dc.date" content="2010-02-01" />
	<meta name="dc.source" content="Nature Reviews Neurology, Published online: 01 February 2010; | doi:10.	1038/nrneurol.2009.217" />
	
	<meta name="citation_publisher" content="Nature Publishing Group" />
	<meta name="citation_authors" content="Agneta Nordberg, Juha O. Rinne, Ahmadul Kadir, Bengt L|[aring]|ngstr|[ouml]	|m" />
	<meta name="citation_title" content="The use of PET in Alzheimer disease" />
	<meta name="citation_date" content="2010-02-01" />
	<meta name="citation_volume" content="6" />
	<meta name="citation_issue" content="2" />
	<meta name="citation_firstpage" content="78" />
	<meta name="citation_doi" content="doi:10.1038/nrneurol.2009.217" />
	<meta name="citation_journal_title" content="Nature Reviews Neurology" />
	<meta name="DCS.dcssip" content="www.nature.com" />
	<meta name="Access" content="No" />
	<meta name="WT.cg_n" content="Nature Reviews Neurology" />
	<meta name="WT.cg_s" content="Review" />
	<meta name="WT.i_dcsvid" content="" />
	<meta name="WT.ec_id" content="" />
	</code>

## 都柏林核心元数据

英文名称: (Dublin Core Metadata,DC)

中文名称: 都柏林核心元数据

网址: http://dublincore.org/

历史背景: 1995 年3 月，由OCLC 与国家超级计算应用中心（NCSA）联合发起，52 位来自图书馆界、电脑网络界专家共同研究产生。目的是希望建立一套描述网络电子文献的方法，以便网上信息检索。后来形成DC元数据标准，其基本方案是包括15 个“核心元素”的集合，由DCMI 负责维护。

<span class="image-600">![](/media/files/2012/mar/4-2.png)</span>

命名: 由于会议的召开地点在美国的俄亥俄州都柏林市,因此这个元数据标准因此得名.

DC核心元数据基本原则:

* 内在性原则,DC元数据只描述对象或实体的内在属性,比如作者,出版日期.是否收费,存取方式属于作品的外在属性,不属于元数据
* 可重复性原则: DC元素集合中的任何元素都是可重复的,比如一篇文献可以有多个作者.
* 可扩展性: 日后可以扩充.
* 可选择性: 使用者可以选择需要的元素来描述数据.比如题名,作者,出版社,类型等.
* 可修饰性原则:可以通过修饰来缩小概念,使描述的粒度更细.

更多资料:

[DC元数据中文wiki网](http://www.dlresearch.cn/dc/index.php/首页)  
[DCMI(Dublin Core Metadata Initiative)](http://dublincore.org/)
 

## PRISM

全称: Publishing Requirements for Industry Standard Metadata

中文: 出版需求工业元数据标准

官方网址: http://www.idealliance.org/specifications/prism/

简介: PRISM是与出版相关的元数据标准.它提供受控此表,让用户在受控的范围内添加meta信息.

结构如图所示

<span class="image-600">![](/media/files/2012/mar/4-3.png)</span>

* PRISM Specification定义了整个框架.
* PRISM Aggregator Message(PAM) Schema/DTD定义了出版商将内容发布到网页,聚合内容时的标准.PAM可以是XML DTD文件也可是XML Schema(XSD),可以十分方便的将内容转化为metadata.
* The newest,specification (XML schema),暂时不了解.待搞明白后再补上此段内容



可以在以下网站查看更多资料:

[PRISM wiki](http://en.wikipedia.org/wiki/Publishing_Requirements_for_Industry_Standard_Metadata)  
[FAQ of PRISM](http://www.prismstandard.org/faq/)


## Metadata of google scholar

Eprints,DSpace,Digital Commons 以及OJS等期刊管理软件,设置后在导出的html中会有<meta>标签.google Scholar支持Highwire Press tags (e.g., citation_title), Eprints tags (e.g., eprints.title), BE Press tags (e.g., bepress_citation_title), and PRISM tags (e.g., prism.title).都柏林核心元数据在描述期刊时粒度太大,无法准确的描述期刊名,卷,期,起止页码,因此应当放到Metadata的最末端.

* 文章标题  citation_title 或者 DC.title元素用于文章的标题,请勿填写杂志名称,数据库名称,书籍名称.该元素对于Google Scholar是必需的(Required)
* 作者 citation_author 或 DC.creator元素用于作者信息,作者的first name与lastname可以颠倒;如果有多个作者,请将他们放到单独的元素中;学位信息,附属机构等信息请勿填写.请勿在此元素中填写网站的拥有者,或者该文的广告赞助商.Google Scholar要求至少有一个作者.
* 出版日期 citation_publication_date 或 DC.issued元素用于描述该文的出版日期.不要把存入数据库的日期(citation_online_date)放到这个元素中.尽量使用详细格式的日期,比如”2010/5/12″.这个元素对于Google Scholar是必须的(Required).
* 杂志或会议 文章必须提供以下详细信息: citation_journal_title [杂志名称]or citation_conference_title[会议名称], citation_issn[ISSN号], citation_isbn[isbn号], citation_volume[卷], citation_issue[期], citation_firstpage[起始页码], and citation_lastpage[截止页码].对于都柏林核心元数据,DC.relation.ispartof[杂志或会议名称],DC.citation.volume[卷], DC.citation.issue[期], DC.citation.spage [起始页], and DC.citation.epage [截止页].无论采用哪种结构化的标引方式,提供的信息必须定位本文.比如在其他文档中我们经常用Adv Enzymol Relat Areas Mol Biol. 2011;78:97-160这种传统方式来著名文献出处.
* 字符 不要使用<,>.
* 全文 Metadata信息仅用于文献的详细页面,如果这篇文献仅含有摘要信息,全文却存储在pdf文件中.请用citation_pdf_url 或 DC.identifier元素标引pdf文件的URL地址.
 

<table>
<tbody>
<tr>
<th>Meta tag name</th>
<th>Meaning</th>
</tr>
<tr>
<td>citation_title(*)</td>
<td>文章标题</td>
</tr>
<tr>
<td>citation_Publication_date(*)</td>
<td>文章出版年</td>
</tr>
<tr>
<td>citation_online_date</td>
<td>The online publication date</td>
</tr>
<tr>
<td>citation_author (*)</td>
<td>作者,可多个</td>
</tr>
<tr>
<td>citation_pdf_url</td>
<td>PDF全文的url</td>
</tr>
<tr>
<td>citation_conference_title</td>
<td>The conference name or the proceedings title (for conference<br />
and workshop papers)</td>
</tr>
<tr>
<td>citation_journal_title</td>
<td>杂志全名 (for journal papers)</td>
</tr>
<tr>
<td>citation_volume</td>
<td>卷 (for journal papers)</td>
</tr>
<tr>
<td>citation_issue</td>
<td>期 (for journal papers)</td>
</tr>
<tr>
<td>citation_issn</td>
<td>ISSN号 (for journal papers)</td>
</tr>
<tr>
<td>citation_isbn</td>
<td>ISBN号</td>
</tr>
<tr>
<td>citation_firstpage</td>
<td>文章起始页</td>
</tr>
<tr>
<td>citation_lastpage</td>
<td>文章截止页</td>
</tr>
<tr>
<td>citation_dissertation_institution</td>
<td>The university name (for master&#8217;s and Ph.D. thesis)</td>
</tr>
<tr>
<td>citation_technical_report_institution</td>
<td>The institution name (for technical reports)</td>
</tr>
<tr>
<td>citation_technical_report_number</td>
<td>The technical report number (for technical reports)</td>
</tr>
</tbody>
</table>

无论选取都柏林核心元数据,prism还是highwire press,你都要提供三个必须的元素(1)文章标题;(2)作者,至少一个;(3)出版年.

如有兴趣可以去google scholar的技术支持页面去了解更详细的介绍.

## 被Google Scholar拒绝的各种理由

1. 时效性

	google 爬虫一周可能会多次爬取新的文献,但是旧文献的更新可能需要3-6个月.如果一个网站有海量数据,那么可能需要几年的时间.

2. 原创

	如果您不是文献的出版商,那么搜索结果中可能不会将您的网站统计在内.

3. 严格遵守rule

	清确保您的站点严格遵守了google scholar要求的Metadata规范.此外每篇文献有独立的URL;必须有文章的摘要信息.

4. 系统的配置是否正确

	如果使用ojs类的期刊管理软件,请确保您的软件设置正确

5. 爬虫

	如果您严格的遵守了google scholar的规范,但是很多文章仍然未出现在google scholar中,那么可能是爬虫的问题.确保您的robot.txt文件允许google scholar收录,网站对爬虫的响应速度过慢?限制爬虫的爬网速度?详细请参考[google的爬虫指南](http://scholar.google.ca/intl/en/scholar/inclusion.html#crawl).

6. U are too small!

	如果您遵循了所有的规则,对爬虫有好,但是经过六个月依然未被google scholar收录,可能是爬虫并没有意识到您的网站的重要性.为了弥补,您可以[手工提交](http://www.google.com/support/scholar/bin/request.py).