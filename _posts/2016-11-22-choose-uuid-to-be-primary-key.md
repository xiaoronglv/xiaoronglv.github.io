---
title: UUID 可以做数据库主键吗？
layout: post
guid: BU7bhsJ84qqaAwwc
date: 2016-11-22 14:04:27
tags:
  - Database
---



## Branching Factor

> 什么是 [Branching Factor](https://en.wikipedia.org/wiki/Branching_factor)?

> In computing, tree data structures, and game theory, the branching factor is the number of children at each node, the outdegree.

简言之，一个节点(node)可以有几个孩子(child)。

MySQL 的 integer type 有四种类型

tinyint
smallint
mediumint
int
bigint

假如 InnoDB 的 page 为16 KB，如果放 integer(4 Byte) 可以容纳 16*1024/4=4096 keys；如果放32位 UUID 可以容纳 16*1024/(32*4) = 128 keys.

> 0b730c06-3650-4841-93d7-f2d01c9ad9f8


如果你的表1,000,000条记录。

integer B+ 树的高度为 log<sub>4096</sub>(1,000,000) = 1.66
UUID B+ 树的高度为 log<sub>129</sub>(1,000,000) 2.84


## size of cluster index

加上对比数据，如何在 mysql 中查询索引的大小呢？


## size of secondary index


加上详细的数据。

加上那个印度人画的图。


## 节点的饱和度(待实验)



## doubly linked list(待证实)



## Reference

1. [MySQL 用 UUID 作为主键，实际使用中有什么问题？]()

2. [Understanding B+tree Indexes and how they Impact Performance](http://www.ovaistariq.net/733/understanding-btree-indexes-and-how-they-impact-performance/#.WDPjkJJ4xtx)

3. Wikipedia: B+ tree

4. Wikipedia: Branching Factor
