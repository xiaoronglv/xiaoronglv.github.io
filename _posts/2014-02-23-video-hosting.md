---
title: 视频托管的那些事儿
layout: post
guid: BD9LxZgm2aYH
tags:
  - Rails
---

<link href="//vjs.zencdn.net/4.4/video-js.css" rel="stylesheet">
<script src="//vjs.zencdn.net/4.4/video.js"></script>

## 场景

薄荷商店在出售一些健康食品：全麦麦片、谷物早餐等等。产品经理想在产品的介绍页面中增加一段商品的视频介绍。

## 方案

我花了半天的时间了解各种视频托管解决方案

1. [CC 视频（bokecc）](http://www.bokecc.com)
2. [优酷开放平台](http://open.youku.com)
3. 云存储，比如[七牛](http://www.qiniu.com) 和 [又拍云](https://www.upyun.com)。

## CC 视频

优点

1. CC 视频后台管理十分简单。把视频传到后台，然后粘贴代码到自己网站的页面就可以了。
2. 视频无广告、无水印

缺点

1. 价格不透明

    销售流程很土鳖，一点不像个互联网公司。注册后需要销售打电话给我，然后报价。我还是喜欢明码标价，自助购买，自助服务。
    
2. 如果支持移动设备，还要额外加钱。
3. 每年最低消费3万。
4. 视频上传后，无法下载原始视频文件。丧失了自主权，心里特别不安全。


## 优酷开放平台

优点：明码标价，公开透明

缺点：视频都被强制打上了优酷的水印
 
备注：优酷按播放次数收费，不按流量。
 
## 云存储


在云存储服务商托管视频的好处是：

1. 收费更合理（主要按流量）
2. 自助服务，流量不够了自己去购买即可，不用和客服唧唧歪歪，浪费时间。
3. 对文件拥有自主权，随时下载原始文件，切换服务商。


### 七牛云存储

七牛提供永久的免费体验用户，业界良心。

1. 免费存储空间1GB
2. 免费每月下载流量1GB
3. 免费每月PUT/DELETE 1万次请求
4. 免费每月GET 10万次请求

> 如果你打算使用七牛，劳烦使用[这个促销链接注册](https://portal.qiniu.com/signup?code=3lgqu6oxgwh1u)，你注册成功后，七牛会送我点流量，多谢。


### 又拍云存储

又拍云提供一周的试用时间。

## 最终的选择

videojs 是一个很棒的 html5 播放器，支持各个版本的浏览器（除了IE6），在移动端的播放体验也很棒。如果浏览器不支持 html5，它会自动转为 flash 播放。

我最终选择将视频托管在 七牛，使用 videojs 播放，给大家展示一下效果。

<video id="example_video_1" class="video-js vjs-default-skin"
  controls preload="auto" width="640" height="264"
  poster="http://mednoter.qiniudn.com/oceans-clip.png"
  data-setup='{"example_option":true}'>
 <source src="http://mednoter.qiniudn.com/oceans-clip.mp4" type='video/mp4' />
 <source src="http://mednoter.qiniudn.com/oceans-clip.webm" type='video/webm' />
 <source src="http://mednoter.qiniudn.com/oceans-clip.ogv" type='video/ogg' />
</video>

