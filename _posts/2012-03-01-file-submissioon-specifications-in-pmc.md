---
title: File Submissioon Specifications in PMC
layout: post
guid: IKrIG7Xogw0j10
tags:
  - 
---
提交文件给PMC遵守的文件规范

## PMC肩负着双重使命:

1. 展示杂志内容;
2. 长时间高质量的存储杂志内容.因此,PMC在设计之初就对文件的提交规范有明确的要求.


## 文件提交规范

1. [XML 文件](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec-xml/):必须有一个编码文章内容的xml文件;
2. [图像](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec-images/): 附上每篇文章的原始高清图片;
3. [PDF](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec/#pdfs): 每篇文章的PDF文件;
4. [相关数据](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec/#supp): 与文章相关的资料,比如表格,视频资料;
5. [上传数据](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec-delivery/): 文件必须按PMC的要求命名,打包.


## XML文件

[Journal Publishing DTD](http://dtd.nlm.nih.gov/publishing)详细的描述了XML文件的结构以及各种元素.PMC Tagging Guideline则详细描述了如何生成符合Journal Publishing DTD规范的XML文件.请查看 [XML Coding Specification](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec-xml/)来了解文章的标引,NIH资助,授权声明,延迟开放等事宜.


**数据中需要的元素**

各种XML文件或者SGML文件都有通用的一些元素,DTD文件中对他们没有要求,但这些元素使得存档变得更加方便.

* ISSN号(Journal ISSN)
* 杂志的编号或者杂志标题缩写(Journal ID or Journal title abbreviation)
* 出版商(Journal Publisher)
* 版权声明(Copyright)
* 卷起页(Volume, issue (if applicable), and article sequence number or pagination)
* 出版日期(Issue Publication date)
* 电子版出版日期(Article electronic publication date)
* 其他,比如部分收录,NIH资助,开放日日期,OA,限制访问.(详细)

## 图像

作者通常提交各种格式的原始图像,比如ppt,pdf,tif,jpg,xml等.出版社为便于印刷或者出版电子版将图片标准化.提交给PMC的图像必须适合存档,高清,足够的宽度,足够的质量是必需的.用于展示的低分辨率图片是达不到要求的.

关于图片,化学式,表格图片质量的详细说明,可以在[Image Quality Specifications](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec-images/)看到介绍.

## PDF文件

Book Review/Letter必须提交与XML或SGML文件相对应的PDF文件.

请优先上传印刷版的PDF,如果杂志没有印刷版,请保证上传的PDF文件要高于以下标准:

1. 线条的dpi800,点的dipi300,色彩的dpi600;
2. 内嵌字体;
3. 图像被无损压缩为Zip或JPEG格式;
4. 插图为矢量图,在转化为位图是不会报错.
 

## 相关数据

PMC要求相关的数据必须以方便的文件格式上传,比如PDF,DOC,CSV等.必须提供数据本身,而不是链接到数据的超链接.提交的相关数据必须遵循以下规范:

文章中未展示的表格,但是记录了重要的实验数据,比如同行评议时必须提交的数据.

 

## 视频

1. PMC鼓励上传视频,有必时降低视频帧数来支持流媒体;
2. 质量低劣的视频经改进后才可上传;
3. 最佳配置:
	1. AAC
	2. 128kbit/s
	3. H.264
	4. 垂直线480
	5. MPEG-4
4. 接收到视频格式 (mov,avi,mpg,mp4,mkv,flv,wmv)
5. 大于1G的视频必须切割为几段.

## 上传

数据必须正确的命名,并且打包为Zip等压缩文件,从而保证能被自动处理.
命名文件,命名压缩包,修订压缩包,上传可以参考[这里](http://www.ncbi.nlm.nih.gov/pmc/pub/filespec-delivery/).

原文: http://www.ncbi.nlm.nih.gov/pmc/pub/filespec/