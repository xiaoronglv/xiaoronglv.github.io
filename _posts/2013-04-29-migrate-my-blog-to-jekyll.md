---
title: 抛弃WordPress，迁移到Jekyll
layout: post
guid: vVaqCnt9VKEp42148
tags:
  - 
---

## 为什么不用WordPress


1. 代码臃肿，功能冗杂

	WordPress是一个很成熟的系统，但是代码质量不好。此外，对于一个独立博客来说，很多功能是冗余的。


2. 文章的保存格式不够纯粹

	以html的格式存储文章，包含样式，结构混乱。一想到正文中掺杂了一堆css样式，我就难以忍受。

## 使用jekyll的好处

Markdown语法就是为写作而生。

今年接手用药助手后台整理工作后，几乎所有的文本都采用Markdown来撰写。写的越多，越喜欢这种简单的标记语言。

1. 内容干净
	
	我们只需要在文章中标记标题，正文，引用，链接，图像。特别的纯粹，没有样式。
	
	文字大小等一些排版细节不应该掺杂在文章中。	
	
	
2. 帖代码方便

	只需要用\<code>包裹代码\</code>即可

3. 逻辑更清楚

	因为在写文章之前要标记标题，结构，所以在写作时，逻辑更清晰。
	
4. 方便转化为其他格式

	使用Pandoc可以非常方便的将Markdown文章转化为PDF, html, 图片，word等等格式。

	
## 我的写作套装

Mou：Mac下最好用的Markdown编辑器

文章托管：Github Pages

Markdown解析器：Jekyll