---
title: ElasticSearch 概念
layout: post
guid: o8OcizGpwN46
date: 2014-07-13 15:00:00
tags:
   - 
---

![ElasticSearch]({{ site.url }}/media/files/2014/jul/13-elasticsearch.jpg)

在学习新知识时，我一般按这个顺序降低学习曲线，提高学习效率。

1. 了解基本概念;
2. 梳理概念之间的联系;
3. 与老知识融汇贯通。


本篇文章是我翻译的 ElasticSearch 的基本概念。


## Index

可以把 ElasticSearch 的 Index 理解为 MySQL 的一个数据库。


## Type

可以理解为 MySQL 中的一个表


## Document

ElasticSearch 中的一个 document 相当于 MySQL 某个表中的一条记录。它是 JSON 结构。

它存储在某个 index（数据库）的某个 type（表）中。

每个 document 都有一个唯一的 id，有不同的字段（field），简直和 MySQL 一模一样。

```
>> index >> type >> document
``` 

## Mapping

可以理解为 MySQL 的表结构（table schema）


## Cluster

很多个 ElasticSearch 的节点（node）可以构成一个分布式的集群（cluster）。


## Shard

每个 index 的数据默认切分到 5 个 shards 中。

有点像 MongoDB，也有点像 MySQL 表的水平分割。


## 参考资料

[Glossay of terms](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/glossary.html)
