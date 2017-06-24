---
title: "MySQL Explain 详解"
layout: post
guid: GM5fCA15AgLibw0q
date: 2017-06-24 22:52:10
coffee: y
tags:
  -
---

本周五做的一次关于 MySQL explain 的技术分享。


## PPT

<script async class="speakerdeck-embed" data-id="c12fac76b5304395a6c7c87f8e9b618a" data-ratio="1.41436464088398" src="//speakerdeck.com/assets/embed.js"></script>


## 大纲

Objective

- give you the hunch: bad query? best query?
- which columns are most important?


Material and Method

- MySQL 5.7.17
- Sakila sample database


Questions

- Shall we add indexes?
- Are indexes efficient?
- What's executation plan of optimizer?
- How to optimize queries?


what does those mean?

- id
- select_type
- table
- access_type
- ref
- rows
- extra


Which columns are more important?

- access type
- rows
- extra


## 录像

视频存放在一个叫 Youtube 的不存在的网站上

<iframe width="560" height="315" src="https://www.youtube.com/embed/3U-qFPlCLlM" frameborder="0" allowfullscreen></iframe>
