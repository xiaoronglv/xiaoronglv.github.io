---
title: Elasticsearch Analyzer 的内部原理
layout: post
guid: 4DUaWeql8Vp6
date: 2014-09-06 23:00:00
tags:
   - 
---


本文将介绍各种 Analyzer，以及他们各种的应用场景。

涉及到的概念

* character filter

* tokenizer

* token filter

* Analyzer

* Term query

## 1 前言

Analyzer 一般由三部分构成，character filters、tokenizers、token filters。

Elasticsearch 有10种分词器（Tokenizer）、31种 token filter，3种 character filter，一大堆配置项。此外，还有还可以安装 plugin 扩展功能。

一旦熟悉了 Analyzer 的原理，我们可以根据需要组装，配置适合我们场景的 Analyzer。


## 2 Analyzer 的组成要素

 Analyzer 就像一条流水线

* Step 1 字符过滤（Character filter）
* Step 2 分词 （Tokenization）
* Step 3 Token 过滤（Token filtering）

原始文本经过处理后变为一堆 Token，比如 「I love Angela」经过 standard analzyer 处理后会转为三个token ["i", "love", "angela"]。


![流水线]({{ site.url }}/media/files/2014/Sep/2014-09-07-flow.png)

这个图，请务必牢记。

Elasticsearch 有 [8个默认的 Analyzer](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/analysis-analyzers.html)。若无法满足我们的需求，我们可以通过「Setting API」构造自己的 Analyzer，举一个简单的例子：

    PUT /my-index/_settings
    {
      "index": {
        "analysis": {
          "analyzer": {
            "customHTMLSnowball": {
             "type": "custom", 
              "char_filter": [
                "html_strip"
              ], 
              "tokenizer": "standard",
              "filter": [
                "lowercase", 
                "stop", 
                "snowball"
              ]  
            }}}}}


这个自定义的 analyzer 会按照以下流程处理文本

* 移除 html 标签 （html_strip character filter）

    比如 \<p> \<a> \<div> 。

* 根据空格分词，去除标点符号（standard tokenizer）

* 把大写的单词转为小写（lowercase token filter）

* 过滤停用词（stop token filter）

    比如 「the」 「they」 「I」 「a」 「an」 「and」。

* 提取词干（snowball token filter，[snowball (雪球算法) ](http://zh.wikipedia.org/wiki/词干提取)是提取英文词干最常用的一种算法。）

    cats  -> cat  
    catty -> cat
    
    stemmer  -> stem  
    stemming -> stem  
    stemmed  -> stem  
    

以上，我们成功的创建了一个 analyzer：customHTMLSnowball

把文本 `The two <em>lazy</em> dogs, were slower than the less lazy <em>dog</em>` 交给 customHTMLSnowball 时，它是如何处理的呢？


![流程图]({{ site.url }}/media/files/2014/Sep/2014-09-07-custom_analyzers_diag.png)


## 3 如何选择一个合适的 Analyzer？

### 3.1 Searching Natural Language 自然语言的全文搜索

#### 3.1.1 词干提取 stemming

当我们的搜索场景为：英文博文、英文新闻、英文论坛帖等大段的文本时，最好使用 stemming token filter，比如 stemmer, snowball, porter_stem 等 token filters.


假如你的系统已经安装了 Elasticsearch，可以试一下 snowball token filter。

    GET http://localhost:9200/_analyze?text=I%20sing%20he%20sings%20they%20are%20singing&analyzer=snowball
    // Output (abbreviated)
    {
      "tokens": [
        {"token": "i", "position": 1, ...},
        {"token": "sing", "position": 2, ...},
        {"token": "he", "position": 3, ...},
        {"token": "sing", "position": 4, ...},
        {"token": "sing", "position": 7, ...},
      ]
    }


snowball 把 sing/ sings / singing 都转化词干 sing。并且自动抛弃了「they」 「are」两个停用词。

词干提取在英文搜索种应用广泛，但是也有很多局限：

1. 词干提取对中文意义不大（毫无意义？）。

2. 有时候词干提取反而让搜索结果变差，比如专业术语，人名。

    eg： flying fish 与 fly fishing 是毫不相关的词，但他们经过 snowball 处理后的词根相同 fli fish，会导致搜索结果不理想。
    
    当需要搜索准确词语时，采用简单的分词策略（不提取词干，只 lowercase）+ Fuzzy query 可能是更好的选择。
 
 
#### 3.1.2 没有空格时，如何分词？  

英文的分词比较简单，根据空格，表单符号就可以分的八九不离十。但是在处理中文、德文时，词与词之间没有空格，使用默认的 analyzer 就不灵光了。

    > curl -XGET 'localhost:9200/_analyze?analyzer=standard&pretty=true' -d '耶稣登山宝训' 
    {
      "tokens" : [ {
        "token" : "耶",
        "start_offset" : 0,
        "end_offset" : 1,
        "type" : "<IDEOGRAPHIC>",
        "position" : 1
      }, {
        "token" : "稣",
        "start_offset" : 1,
        "end_offset" : 2,
        "type" : "<IDEOGRAPHIC>",
        "position" : 2
      }, {
        "token" : "登",
        "start_offset" : 2,
        "end_offset" : 3,
        "type" : "<IDEOGRAPHIC>",
        "position" : 3
      }, {
        "token" : "山",
        "start_offset" : 3,
        "end_offset" : 4,
        "type" : "<IDEOGRAPHIC>",
        "position" : 4
      }, {
        "token" : "宝",
        "start_offset" : 4,
        "end_offset" : 5,
        "type" : "<IDEOGRAPHIC>",
        "position" : 5
      }, {
        "token" : "训",
        "start_offset" : 5,
        "end_offset" : 6,
        "type" : "<IDEOGRAPHIC>",
        "position" : 6
      } ]
    }


比较理想的处理结果应该为 「耶稣登山宝训」 -> ["耶稣", "登山宝训"]。此时我们需要借助一些插件（plugin）来处理中文的分词。

[mmseg](https://github.com/medcl/elasticsearch-analysis-mmseg) 是处理中文一个比较靠谱的插件。

### 3.2 Searching Tokens Exactly 精准搜索

当我们搜索用户名(username)，商品分类（category），标签（tag）时，希望精准搜索。此时最好不要再分词、也不要提取词干。

创建建索引时，在某个字段的 mapping 中指定 "index": "not_analyzed"，从而跳过 analyzer，直接把原始文本转为 term。

| 文本（username）         |  standard analyzer（不推荐）  |  not_analyzed  （推荐）|
| ------------ | -------------       | -------------  | 
| xiaoronglv   | xiaoronglv          | xiaoronglv     |
| angela_liu   | ["angela", "liu"]   | angela_liu     |
| Vincent_xie  | ["vincent", "xie"]  | Vincent_xie    |



然后，使用 Term query 精确搜索

    {"term":{"username":"xiaoronglv"}}



### 3.3 基于发音的搜索

这一段几乎用不着，就不翻译了。

Phonetic analyzers are a powerful tool for dealing with things like real names and usernames. These can be used by installing the elasticsearch-analysis-phonetic plugin. The goal of a phonetic analyzer, like metaphone or soundex is to convert the source text into a series of tokens that represent syllabic sounds. This lets users find things that sound like the query text. For instance, when searching a list of names, one might want a query for the user with the name ‘schmidt’ to be matched if a user searches for the term ‘terminates’. The spelling difference doesn’t matter because both words sound the same.

An example of a search with a metaphone component can be seen in this example on Play. Notice that the search for ‘schmit’ matches both the names ‘schmidt’ and ‘schmitt’. The use of an exact matching ‘not_analyzed’ field is critical for ensuring that exact matches always come first. In the second query in the example, where there is an exact match for ‘schmidt’, the exact match winds up being scored much higher to ensure its primacy in the results. For a more complete treatment of this sort of search see this forum thread on name searches by Elasticsearch developer Clinton Gormley.


> 
> 原文: [《All About Analyzer, Part One》](https://www.found.no/foundation/text-analysis-part-1/) 