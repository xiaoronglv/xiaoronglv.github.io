---
title: OAuth flow chart
layout: post
guid: 4QnNKKE4vCkV
tags:
  - 
---
<span class="image-1000">![](/media/files/2013/apr/06.png)</span>

我以前折腾过Twitter 与 Facebook的第三方登录，每次用户登录时都要会弹出授权页面，用户需要一次次的点击 allow authorization 才能登录。Sam前几天给内部的Redmine系统增加了google OAuth 登录，一切运行良好。

于是我很纠结，为什么我的  app 就不行？

今天上午梳理了一下登录流程，导致我的app重复出现授权页面的原因可能为：  
我的app在本地，callback的域名也是0.0.0.0:3000，google压根没在它的服务器记录这条授权信息吧。