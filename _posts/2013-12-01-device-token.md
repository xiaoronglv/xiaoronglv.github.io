---
title: 不同App获得的device token是否相同？
layout: post
guid: I1021YC2TTWd
tags:
  - 
---

<span class="image-800">![network]({{ site.url }}/media/files/2013/dec/1-apn.jpeg)</span>

这周在学习苹果的消息推送（Apple Push Notification），官方画的流程图很清晰，但是对里面的一个概念 device token 却语焉不详。

读完冗长的文档，唯一有用的却是一个注意事项：

> An application should register [with APN servers] every time it launches and give its provider the current token.  
> 每次应用被打开时，开发者都要重新收集当前设备的 device token，因为它可能变了哦。

stackoverflow 针对[Device token 什么时候会发生变化](http://stackoverflow.com/a/7999690/1153223)有个很棒的解答。

1. 在一台设备中， device token 是系统级别的，不同 App 获得的 device token 是相同的。

    假如我的手机安装了 Angry Bird 和 Evernote ，这两个应用获得 device token 一模一样。

2. device token 并不会因为单个 app 的更新而发生改变。

    假如我的 iPhone 升级了最新版的愤怒的小鸟，这并不会导致我 device token 的改变。

3. 假如我的 iPhone 从 backup 中恢复数据，device token 不会发生变化。

4. 用户抹除 iPhone 的数据时，意味着要与这台手机撇清关系，比如出售或者送人。此时为了保护隐私，device token 会改变。

## 总结

还是老老实实的收集最新的 device token，这是最保险的做法。


## 参考资料

1. [Does the device token ever change, once created?](http://stackoverflow.com/questions/6652242/does-the-device-token-ever-change-once-created/7999690#7999690)

2. [Are push notification tokens unique across all apps for a single device?](http://stackoverflow.com/questions/2751481/are-push-notification-tokens-unique-across-all-apps-for-a-single-device)
