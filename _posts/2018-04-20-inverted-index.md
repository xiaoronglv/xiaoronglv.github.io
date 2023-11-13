---
title: 反向索引，为什么称之为反向索引？
layout: post
guid: oc1TThEF1HlV2Isyr
date: 2018-04-20 10:20:07
tags:
  -
---

在谈论搜索引擎的索引时，会涉及到两个概念正向索引(forward index)和反向索引(inverted index)。

听上去，它们像是一种全新数据结构。让我们看看维基百科的解释。

## 正向索引：从文档到单词。

假如有三个 txt 文档，

- Document_1: The cow says moo.
- Document_2: The cat and the hat.
- Document_3: The dish ran away with the spoon.

解析每个文档出现的单词，然后建立从文档 (document) 到词组 (words) 的映射关系，这就是正向索引。

| Document | Words  |
|:--|:--|
| Document_1 | the,cow,says,moo |
| Document_2 | the,cat,and,the,hat |
| Document_3 | the,dish,ran,away,with,the,spoon  |

## 反向索引：从词到文档。

反向索引方向则是正向索引的逆向，建立从单词 (word) 到文档(document lsit) 的映射关系。

| Word | Documents |
|:--|:--|
| the | Document_1, Document_3, Document_4, Document_5, Document_7 |
| cow |Document_2, Document_3, Document_4  |
| says | Document_5 |
| moo  | Document_6 |

怎么看这都不是什么全新的数据结构啊？

## 现实世界中的索引

使用 index 快速检索信息并不是21世纪新的发明。

1876年，杜威发明图书分类法(Dewey Decimal Classification)。我们通过这个方法，给图书建立索引，按书架快速查找(get)和摆放(set)书籍。

| 分类 | 图书 |
|:--|:--|
| 哲学与心理学 | 《苏菲的世界》，《理想国》 |
| 宗教 | 《圣经》，《古兰经》  |
| 社会科学 | 《宏观经济学》，《微观经济学》 |
| ... | ... |


通常书籍最后的章节会罗列所有涉及到的行业术语，你可以通过这个列表查看哪一页交代了这些术语，并快速跳转到定义的地方，这也是一种索引 `word -> page`。

![](/media/files/2018/2018-04-27-glossary.jpg)

## 问题

index 从来都不是一个新发明，从19世纪的图书分类法，到21世纪的数据库索引，我们一直在用。而且当大家提起`索引`这个术语时，脑海中都是按 `word -> document` 方向来建立。

反向索引，其实就是普通的索引嘛。

为什么有人仍然坚持创建出一个新的术语 inverted index ？

此外 forward index 在现实世界中根本毫无用处，压根不能称之为索引，为什么要造这个词？

无论是 Stack Overflow 还是 ElasticSearch 的官方文档也只是一而再，再而三的强调「是什么」，并没有回答「为什么」[1]。

最终 Stack Overflow 一个不起眼的回答道出了玄机。[2]

> 索引(index) 是个很老的概念。但是搜索引擎横空出世之后，却把这个概念包装了一下，称之为「反向索引」。并不是有什么本质性的区别，乃是因为搜索引擎在创建会先解析文档的关键词，创建一个正向索引，然后再基于正向索引，把方向逆转过来，创建反向索引，仅此而已。

> This should be the accepted answer as it answers the question why we call an index "inverted" even if it is just what everybody thinks of a "normal index". A SQL b-tree index stores for each word a pointer to all rows ("documents") containing it. There we call it "index". **But in search engines we suddenly call this exact same procedure "inverted index". Not because it's fundamentally different, but because we first created a "forward index" (split text) and then "inverse" it. So, all in all, the name "inverse" comes from the process of creating it, not from the final structure of the index.**

## 搜索引擎如何创建索引？

如果要进一步理解正向索引，需要了结搜索引擎 analyzer 的基本工作步骤。所有的文档在建立索引之前都要经过一个 pipeline。

![](/media/files/2018/2018-04-23-analyzer.svg)

1. character filter，过滤一些词，比如 html 标签。
2. tokenization。分词，转换时态，转换单复数等。
3. token filter。过滤一些token，比如 the，a，an，this，these 等停用词。

最终 pipeline 的输出就是一个单词列表。我猜测这个单词列表就用来创建正向索引。

## 结论

说到底，反向索引就是生活中处处可见的普通索引。

反向索引之所以在搜索引擎中中被冠以新词，是因为在它之前要先创建正向索引。


## Reference

1. [Elasticsearch: Inverted Index](https://www.elastic.co/guide/en/elasticsearch/guide/current/inverted-index.html)

2.  Stack Overflow 的[这个回答](https://stackoverflow.com/questions/7727686/whats-the-difference-between-an-inverted-index-and-a-plain-old-index/8391145#8391145) 没有被选为最佳答案，真是可惜。
