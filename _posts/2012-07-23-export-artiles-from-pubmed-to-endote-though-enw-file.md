---
title: PubMed to EndNote by enw file
layout: post
guid: 7bLfAjXdFONG
tags:
  - 
---

enw文件是参考文献通用标准，与各种文献管理软件兼容好。比如这个文件test.enw，下载后双击即可导入到参考文献管理软件endnote、papers等。enw文件会调用EndNote中对应filter（EndNoter Import.enf），将不同字段的内容填充到endnote中对应的字段。Mac下的参考文献管理软件Papers也是采用这种原理。科研著作有多种类型，journal article、book、conference paper等。由于PubMed为医学文献数据库，因此类型全部为journal article。
本文重点描述，如何将PubMed中的文献导出为正确的enw文件。
  
编码：utf8
扩展名：enw


    %0 Journal Article
    %A Author
    %J Journal
    %C {IGNORE}
    %D Year
    %E {IGNORE}
    %F Label
    %G Language
    %H Translated Author
    %I {IGNORE}
    %K Keywords
    %L Call Number
    %M Accession Number
    %N Issue
    %O Alternate Journal
    %P Pages
    %Q Translated Title
    %R DOI
    %S {IGNORE}
    %T Title
    %U URL
    %V Volume
    %W Database Provider
    %X Abstract
    %Y {IGNORE}
    %Z Notes
    %1 Legal Note
    %2 PMCID
    %3 {IGNORE}
    %4 {IGNORE}
    %6 {IGNORE}
    %7 Epub Date
    %8 Date
    %9 Type of Article
    %? {IGNORE}
    %@ ISSN
    %! Short Title
    %# {IGNORE}
    %$ {IGNORE}
    %] {IGNORE}
    %& Start Page
    %( Original Publication
    %) Reprint Edition
    %* Reviewed Item
    %+ Author Address
    %^ Caption
    %> File Attachments
    %< Research Notes
    %[ Access Date
    %= {IGNORE}
    %~ Name of Database


XML是自我描述性文档，PubMed中的每篇文献都有xml版本，我们可以基于XML生成enw文件。本文中以Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome differentiation: a randomized controlled trial这篇文章为例，介绍整个转化流程。

## XML的获取

利用NCBI的公共接口Eutility获取该文献的xml文件

    http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&id=22805083&rettype=xml


备注：  
db=PubMed 数据库为pubmed，  
id=22805083 id为22805083对应的文献。  

## 生成enw文件

1. 文章类型（%0)

所有pubmed中导出的著作类型都是journal article，因此默认的值：

    %0 Journal Article

2. 作者

	1. 如果有多个作者，每个作者开头用%A，如：

    	%A 张三    
    	%A 李四
    	%A 吕小荣

	2. 作者（%A）Author

		这篇文献http://www.ncbi.nlm.nih.gov/pubmed/22805083 有12个作者，作者在endnote中保存的格式为：姓+ForeName首字母，ForeName首字母之间用“点”隔开。
    
                Lin, S.M.
                Zhou, R.Q.
                Tang, W.J.
                Huang, P.X.
                Dong, Y.
                Wang, J.
                Yu, Z.H.
                Chen, J.L.
                Wei, L.
                Xing, S.L.
                Cao, H.J.
                Zhao, H.B.

	如果采用这种样式排版，文章中出现的引文样式丑陋，如下所示：
	
		Lin S.M.,Zhou R.Q.,Tang W.J., et al. Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome differentiation: a randomized controlled trial. Zhong Xi Yi Jie He Xue Bao. 2012 Jul;10(7):766-76.

	然而大多数杂志引文的排版格式为：姓+Initials 
	
		Lin SM, Zhou RQ, Tang WJ, et al. Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome differentiation: a randomized controlled trial. Zhong Xi Yi Jie He Xue Bao. 2012 Jul;10(7):766-76.

	因此这个作者对应的正确板式应该为： Lin SM（LastName + Initials）
	
	    <Author ValidYN="Y">
	    <LastName>Lin</LastName>
	    <ForeName>Shui-Miao</ForeName>
		<Initials>SM</Initials>
	    </Author>

3. 期刊 （%J）Journal

	引文格式一般用杂志简写，因此这个字段应为此杂志的ISO abbreviation

    	%J Zhong Xi Yi Jie He Xue Bao

4. 年（%D）Year

		%D 2012

	取这个节点的数据：
	Article => Journal => ISSN => JournalIssue =>PubDate => Year

	    <Article PubModel="Print">
	    <Journal>
	    <ISSN IssnType="Print">1672-1977</ISSN>
	    <JournalIssue CitedMedium="Print">
	    <Volume>10</Volume>
	    <Issue>7</Issue>
	    <PubDate>
	    <Year>2012</Year>
	    <Month>Jul</Month>
	    </PubDate>
	    </JournalIssue>
	    <Title>Zhong xi yi jie he xue bao = Journal of Chinese integrative medicine</Title>
	    <ISOAbbreviation>Zhong Xi Yi Jie He Xue Bao</ISOAbbreviation>
	    </Journal>
	    <ArticleTitle>[Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome     differentiation: a randomized controlled trial].</ArticleTitle>
	    <Pagination>
	    <MedlinePgn>766-76</MedlinePgn>
	    </Pagination>
	    </Article>

5. 语言（%G）Language

	    %G eng

		<Language>eng</Language>

6. 期（%N）Issue

	    %N 7

	取这个节点的数据：
	Article => Journal => ISSN => JournalIssue =>Issue

	      <Article PubModel=”Print”>
          <Journal>
          <ISSN IssnType=”Print”>1672-1977</ISSN>
          <JournalIssue CitedMedium=”Print”>
          <Volume>10</Volume>
          <Issue>7</Issue>
          <PubDate>
          <Year>2012</Year>
          <Month>Jul</Month>
          </PubDate>
          </JournalIssue>
          <Title>Zhong xi yi jie he xue bao = Journal of Chinese integrative medicine</Title>
          <ISOAbbreviation>Zhong Xi Yi Jie He Xue Bao</ISOAbbreviation>
          </Journal>
          <ArticleTitle>[Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome      differentiation: a randomized controlled trial].</ArticleTitle>
          <Pagination>
          <MedlinePgn>766-76</MedlinePgn>
          </Pagination>
          </Article>

     
7. 页码（%P）Pages

		%P Pages

	取这个节点的数据：
	Article => Pagination => MedlinePgn

        <Article PubModel=”Print”>
        <Journal>
        <ISSN IssnType=”Print”>1672-1977</ISSN>
        <JournalIssue CitedMedium=”Print”>
        <Volume>10</Volume>
        <Issue>7</Issue>
        <PubDate>
        <Year>2012</Year>
        <Month>Jul</Month>
        </PubDate>
        </JournalIssue>
        <Title>Zhong xi yi jie he xue bao = Journal of Chinese integrative medicine</Title>
        <ISOAbbreviation>Zhong Xi Yi Jie He Xue Bao</ISOAbbreviation>
        </Journal>
        <ArticleTitle>[Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on    syndrome     differentiation: a randomized controlled trial].</ArticleTitle>
        <Pagination>
        <MedlinePgn>766-76</MedlinePgn>
        </Pagination>
        </Article>

8. DOI（%R）

	    %R 10.1007/s00248-011-9913-9

	取ArticleIdList => ArticleId (IdType=”doi”)的值

	       <ArticleIdList>
	       <ArticleId IdType=”doi”>10.1007/s00248-011-9913-9</ArticleId>
	       <ArticleId IdType=”pubmed”>21805083</ArticleId>
	       </ArticleIdList>


9. 标题 （%T）Title

	    %T Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome differentiation: a randomized controlled trial.

	取Article => ArticleTitle 中的值，如果标题被中括号包裹，则去掉两端的中括号。
	
		<ArticleTitle>[Chinese herbal medicine for patients with mild to moderate Alzheimer disease based on syndrome differentiation: a randomized controlled trial].</ArticleTitle>

10. 超链接（%U）URL

	每个URL另起一行
	
	    %U http://pubmed.cn/22805083
	    %U http://openurl.ebscohost.com/linksvc/linking.aspx?

	URL应该包括两部分：
	
	1. PubMed中的链接 http://pubmed.cn/22805083
	2. 该文献linkout的超链接

11. 卷（%V）Volume

	    %V 10

	取Article => Journal => JournalIssue => Volume中的数据

12. 数据提供方（%W）

	    %W PubMed.cn

13. 摘要（%X）abstract

	    %X Abstract

	取<Abstract>…</Abstract>节点内的数据

14. ISSN (%@)

	ISSN多个时的处理方法如下：

		%@ 1872-7972 (Electronic)
		%@ 1872-7972 (Print)
		%@ 0304-3940 (Linking)

15. 作者单位 （%+）Author Address

	    %+ Shanghai Geriatric Institute of Chinese Medicine, Shanghai 200031, China; E-mail: trastayl@hotmail.com.

	数据取自这个节点
	
	    <Affiliation>Shanghai Geriatric Institute of Chinese Medicine, Shanghai 200031, China; E-mail: trastayl@hotmail.com.</Affiliation>