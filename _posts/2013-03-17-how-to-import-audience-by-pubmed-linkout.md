---
title: 如何制作医学期刊的PubMed LinkOut文件 ?
layout: post
guid: uD9P4iAGJh5L161
tags:
  - 
---

一本医学期刊的生命里有两个重要事件。第一步，被PubMed收录；第二步，被SCI收录。我有幸见证了JTD杂志的成长之路，在此过程中提供了一些技术支持。今天我这篇博文的主题是 —— 如何制作PubMed LinkOut文件。
本文适用于已被PubMed收录，期望通过PubMed导入网站，增加期刊影响力的医学期刊。

## 什么是Linkout？

众所周知，PubMed是一个摘要数据库，并不收录全文。PubMed做了一个两全其美的方案，方便用户下载全文，同时推广出版商的品牌，即通过LinkOut 链接到出版商网站。比如Plos One的这篇文章：

<span class="image-1200">![](/media/files/2013/mar/17-1.png)</span>


## LinkOut对期刊有哪些好处？

1. 影响力
	在PubMed的网站上展示自己杂志的banner，免费的广告，不做可惜。
2. PR值
	PubMed的PR值为9，导入大量的外链可显著增加自己网站的PR值
3. 导入流量
	吸引用户点击，跳转到出版商的网站，从而吸引用户阅读更多的内容，提高转化率。

## 如何制作LinkOut文件？

LinkOut文件其实就是一个链条，一边是PubMed的id，一边是出版商的url。 这个关系是如何构造的呢。LinkOut文件包括两部分：

###identity file

里面包括出版商的各种信息，名称，缩写，id，简介等等。JTD杂志的identity file:

  
    <?xml version="1.0"?>
    <!DOCTYPE Provider PUBLIC "-//NLM//DTD LinkOut 1.0//EN"
    "http://www.ncbi.nlm.nih.gov/projects/linkout/doc/LinkOut.dtd">
    <Provider>
        <ProviderId>8410</ProviderId>
        <Name>Pioneer Bioscience Publishing Company</Name>
        <NameAbbr>pioneerpub</NameAbbr>
        <SubjectType>publishers/providers</SubjectType>
        <Url>http://www.thepbpc.org</Url>
    </Provider>




### resouces file

里面包括PubMed的每篇文章如何与出版商的网址建立关系。JTD杂志通过doi做了跳转，不用一次次的提交resource文件。

    <?xml version="1.0" ?>
    <!DOCTYPE LinkSet PUBLIC "-//NLM//DTD LinkOut 1.0//EN"
      "http://www.ncbi.nlm.nih.gov/projects/linkout/doc/LinkOut.dtd">
    <LinkSet>
      <Link>
       <LinkId>jtd</LinkId>
       <ProviderId>8410</ProviderId>
       <IconUrl>http://www.jthoracdis.com/public/system/jtd/icon-for-pubmed.jpg</IconUrl>
       <ObjectSelector>
        <Database>PubMed</Database>
        <ObjectList>
          <Query>"2072-1439"[ta] AND 2009:2030[dp]</Query>
        </ObjectList>
      </ObjectSelector>
      <ObjectUrl>
        <Base>http://dx.doi.org/</Base>
        <Rule>&lo.doi;</Rule>
        <Attribute>full-text online</Attribute>
      </ObjectUrl>
    </Link>
    <Link>
      <LinkId>jgo</LinkId>
      <ProviderId>8410</ProviderId>
      <IconUrl>http://www.thejgo.org/public/system/jgo/icon-for-pubmed.jpg</IconUrl>
      <ObjectSelector>
        <Database>PubMed</Database>
        <ObjectList>
          <Query>"2219-679X"[ta] AND 2009:2030[dp]</Query>
        </ObjectList>
      </ObjectSelector>
      <ObjectUrl>
        <Base>http://dx.doi.org/</Base>
        <Rule>&lo.doi;</Rule>
        <Attribute>full-text online</Attribute>
      </ObjectUrl>
    </Link>
    <Link>
     <LinkId>cjcr</LinkId>
     <ProviderId>8410</ProviderId>
     <IconUrl>http://www.thecjcr.org/public/system/cjcr/cjcr-icon-pubmed.jpg</IconUrl>
     <ObjectSelector>
      <Database>PubMed</Database>
      <ObjectList>
        <Query>"1000-9604"[ta] AND 2009:2030[dp]</Query>
      </ObjectList>
    </ObjectSelector>
    <ObjectUrl>
      <Base>http://dx.doi.org/</Base>
      <Rule>&lo.doi;</Rule>
      <Attribute>full-text online</Attribute>
    </ObjectUrl>
    </Link>
    <Link>
     <LinkId>qims</LinkId>
     <ProviderId>8410</ProviderId>
     <IconUrl>http://www.amepc.org/public/system/qims/qims-icon-pubmed.jpg</IconUrl> 
     <ObjectSelector>
      <Database>PubMed</Database>
      <ObjectList>
        <Query>"2223-4306"[ta] AND 2009:2030[dp]</Query>
      </ObjectList>
    </ObjectSelector>
    <ObjectUrl>
      <Base>http://dx.doi.org/</Base>
      <Rule>&lo.doi;</Rule>
      <Attribute>full-text online</Attribute>
    </ObjectUrl>
    </Link>
    </LinkSet>



## 相关资料

1. [PubMed：提供全文的出版商如何制作LinkOut文件？](http://www.ncbi.nlm.nih.gov/books/NBK3812/)
2. [PubMed：LinkOut文件技术细节](http://www.ncbi.nlm.nih.gov/books/NBK3807/#files.LinkOut_DTD)
3. [JTD linkout source code](https://github.com/xiaoronglv/pubmed_linkout_example)



我也是第一次制作LinkOut文件，抛砖引玉，欢迎有兴趣的同学一起讨论。