---
title: Sidekiq frozen worker
layout: post
guid: gdvgbzktxp59
date: 2015-01-15 22:05:11
tags:
   - Ruby
---

![Frozen worker]({{ site.url }}/media/files/2015/Jan/2015-01-15-frozen-car.jpg)


今天生产环境的 Sidekiq 出现了一件奇怪的事情：16个线程都处于 busy 状态。

```
[boohee@tiger ~]$ ps aux|grep plan
boohee   sidekiq 3.2.6 plan [16 of 16 busy]                                                                                                                                                                        

```
所有的异步任务都卡住了，sidekiq.log 中也没有任何动静。

到底出了什么问题呢？我开始猜。

## 假设1：Sidekiq 进程死掉了？


tiger中的 sidekiq 线程全忙。

```
[boohee@tiger ~]$ ps aux|grep plan
boohee   sidekiq 3.2.6 plan [16 of 16 busy]                                                                                                                                                                        

```

lion 中的 sidekiq 线程全忙

```
[boohee@lion ~]$ ps aux|grep plan
boohee   sidekiq 3.2.6 plan [16 of 16 busy]                                                                                                                                                                        

```

两台 server 的 sidekiq 同时假死，不可能这么巧吧。

我用 Capistrano 重启 Sidekiq，重启成功。（Sidekiq 对信号还是有反应的）

但5分钟之后 sidekiq 的线程又全部处于 busy 状态。


于是我又猜想，是不是某个很耗时的任务把所有的线程都阻塞了？


## 假设2：某个很耗时的任务把所有的线程都阻塞了？


Google 后找到了 [Sidekiq Problems and Troubleshooting](https://github.com/mperham/sidekiq/wiki/Problems-and-Troubleshooting) 这篇文档，里面写了两种常见的 Frozen worker 的情况：

If all your workers are "frozen" or no jobs seem to be finishing, it's possible a remote network call is pending forever. This is common in two scenarios:

1. DNS lookup - resolving a hostname might hang. This has a serious side effect in MRI of locking up everything because of the way MRI uses DNS by default. The solution is to run require 'resolv-replace' in your initializer, which installs a pure Ruby DNS resolver that works concurrently.

2. Net::HTTP - unresponsive remote servers can cause a Net::HTTP call to hang and lock up your workers. Set open_timeout to ensure your code raises an exception rather than hanging forever.



看完了文档，我立马就想到了代码中的问题。


```
class XiaomiUserProjectNotifier
  include Sidekiq::Worker
  sidekiq_options retry: 3

  def perform(project_id)
    
    # 此处忽略一些业务代码
    
    begin
      res = RestClient.post url, params
    rescue => err
      raise err
    ensure

    # 此处忽略一些业务代码
  end
end
```

我使用 RestClient 调用了小米的接口，但这个接口不太稳定。当小米的接口没有响应时，Sidekiq 线程就在那傻傻的等待，几分钟...几个小时...几天...

最终，所有的线程都因为这个问题沦陷了（16 of 16 busy）


## 解决方法

知道问题，解决起来就简单了。

把 Restclient 超时时间设定为 10秒，发布上线，这个问题就解决了。


```
class XiaomiUserProjectNotifier
  include Sidekiq::Worker
  sidekiq_options retry: 3

  def perform(project_id)
    
    # 此处忽略一些业务代码
  
    begin
      res = RestClient::Request.execute(
        :method => :post,
        :url => url,
        :payload => params,
        :timeout => 10,
        :open_timeout => 10
      )
    rescue => err
      raise err
    ensure

    # 此处忽略一些业务代码
  end
end
```



## 总结

异步任务中调用外部接口时，一定要加超时，否则 Sidekiq 有被冻住的风险。


> 写完本文，我还有个疑惑：
> 
> * Rails 有没有 require 'resolv-replace'？
> * 需要在 sidekiq 中 require 'resolv-replace' 吗？


