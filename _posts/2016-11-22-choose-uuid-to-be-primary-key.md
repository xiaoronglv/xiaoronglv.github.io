---
title: "猜想: 如果拿UUID做数据库主键..."
layout: post
guid: 7Nn97mu797lT
date: 2016-11-22 14:04:27
tags:
  - Database
---

有人请教过我如果在生产环境「拿UUID做数据库主键」，会导致什么后果。

我从未这么干过，但是会从这几个角度考量。


## 1. Branching Factor

> 什么是 [Branching Factor](https://en.wikipedia.org/wiki/Branching_factor)?

> In computing, tree data structures, and game theory, the branching factor is the number of children at each node, the outdegree.

简言之，一个节点(node)可以有几个孩子(child)。

假如 InnoDB 的 page 为16 KB，如果放 integer(4 Byte) 可以容纳 16\*1024/4=4096 个 children；

如果放32位 UUID 可以容纳 16\*1024/(32*4) = 128 children.


![](/media/files/2018/2018-07-21-800px-Bplustree.png)

对于一个 1,000,000 条记录的表：

- Integer B+ 树的高度为 log<sub>4096</sub>(1,000,000) = 1.66

- UUID B+ 树的高度为 log<sub>129</sub>(1,000,000) 2.84

**猜测：对 Read 的性能不会有太大影响。**

## 2. 对 cluster index 的影响

What is cluster index?

Clustered Indexes:  In clustered index, the non-leaf level points to the actual data.

![](/media/files/2018/2018-07-21-Clustered-Indexes.png)

猜测：

1. UUID 是无序的，每一次 Insert 都是磁盘的 Random write。写入效率降低。
2. 可能会导致 B+ 的节点分裂，引起连锁反应。写入效率变低，或更糟。


## 3. 对 secondary index 的影响

What's secondary index?

Non-Clustered Indexes: In Non-Clustered index the leaf nodes point to primary key, which then point to actual data.


All indexes other than the clustered index are known as secondary indexes. In InnoDB, each record in a secondary index contains the primary key columns for the row, as well as the columns specified for the secondary index. InnoDB uses this primary key value to search for the row in the clustered index.

![](/media/files/2018/2018-07-21-Non-Clustered-Indexes.png)


**猜测：secondary index 的 leaf 默认会指向主键。如果主键是 32 byte 的UUID，那么所有的 secondary key 的大小就会翻8倍。**


## Reference

- [Understanding B+tree Indexes and how they Impact Performance](http://www.ovaistariq.net/733/understanding-btree-indexes-and-how-they-impact-performance/#.WDPjkJJ4xtx)

- [Wikipedia: B+ tree](https://en.wikipedia.org/wiki/B%2B_tree)

- [Wikipedia: Branching Factor](https://en.wikipedia.org/wiki/Branching_factor)

- [MySQL: Clustered and Secondary Indexes](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)

- [Geek Philip: Clustered Indexes v/s Non-Clustered Indexes](http://www.geekphilip.com/2013/06/14/clustered-indexes-vs-non-clustered-indexes/)