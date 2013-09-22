---
title: 查询 Rails 生产环境下日志
layout: post
guid: wSBONVRQe6Al1
tags:
  - 
---

前两天做了个小应用，部署到Linode上后一直提示500错误。

第一次在生产环境中排查问题，一时没有头绪。后来恍然大悟：笨蛋，查log啊！可生产环境中Rails的log在哪个位置呢？

假如你是用Capistrano部署Rails应用，log应该在这个位置：

<pre><code>.../appname/shared/log</code></pre>