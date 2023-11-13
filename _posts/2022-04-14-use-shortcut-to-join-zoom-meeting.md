---
title: "iPhone 一键拨入 zoom 会议"
layout: post
guid: eeTBpH9uhgoQsy8ujRVw
date: 2022-04-14 20:00:00
tags:
  -
---

![](/media/files/2022/2022-04-14-7.jpg)

> Join Zoom Meeting  
> https://us04web.zoom.us/j/72358911786?pwd=OScDs3rdFTjs  
>
> Meeting ID: 723580786  
> Passcode: vu3128e

我儿子每周用 Zoom 上网课，老师会提前发短消息给我 Zoom 链接，然后我打开 iPad，点击链接，自动跳转到浏览器，然后再跳转到 zoom，开始上课。

因为我的短信太多，儿子才6岁，不会操作，每次都要找我。有没有办法把老师的 Zoom 链接保存到手机屏幕上，每次上课时点击一夏图标就自动打开呢？

我最终在 reddit 上找到了办法。

##  制作 Zoom URL

普通的网址长这个样子 https:://google.com，人们可以在浏览器中打开。

iPhone / iPad 提供了一种特殊的格式网址，可以引导用户从一个 App 跳转到另外一个 App。

Zoom 的链接格式如下：

```
zoomus://zoom.us/join?confno=MEETINGNUMBER&pwd=PASSWORD
```

- confno 是会议室的号码
- pwd 是会议室的密码。注意，密码前面是一个 `&`，我一开始复制网址时错误的复制为 `?`

现在我们可以把正文开头的那个非常长的网址改造一下。每次点击这个链接，手机会自动打开 Zoom，并进入老师的会议室。

```
zoomus://zoom.us/join?confno=723580786&pwd=OScDs3rdFTjs
```


## 把快捷键添加到桌面

搞定了网址，下一步就是把这个网址放到屏幕上。我们可以利用 iPhone/iPad 的 "快捷指令" 创建一个快捷键，然后放到桌面。

1. 打开 [快捷指令](https://apps.apple.com/sg/app/%E5%BF%AB%E6%8D%B7%E6%8C%87%E4%BB%A4/id915249334?l=zh) 应用。

2. 创建快捷键，添加 'Open URLs' Action

    ![](/media/files/2022/2022-04-14-5.jpg)

    注意事项: 千万不要使用 x-callback 这种类型的 Action，否则会导致 shortcut 卡住。

3. 把这个快捷键添加到桌面
     ![](/media/files/2022/2022-04-14-6.jpg)
4. 完工！只要点击图标，就自动拨入老师的会议室了。小朋友也有能力自己操作了，不用麻烦大人了。


这是视频教程

<iframe src="//player.bilibili.com/player.html?bvid=BV1SS4y1w7Bf&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="100%" height="600"> </iframe>


## 参考资料

1. [reddit: 如何使用快捷键一键拨入 Zoom 会议室？](https://www.reddit.com/r/shortcuts/comments/kwcli3/comment/gj4mhrr/?utm_source=share&utm_medium=web2x&context=3)
2. [reddit: shortcut 卡住是怎么回事？](https://www.reddit.com/r/shortcuts/comments/q6fcco/open_xcallback_url_doesnt_end_why/)
3. [reddit: 什么是 iOS x-callback？](https://www.reddit.com/r/shortcuts/comments/aktjgk/what_is_xcallback_and_what_does_it_do/)




