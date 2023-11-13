---
title: "Rails: 如何给每个请求的 log 加上 UUID?"
layout: post
guid: KMYoTm92MjoXMX7FoIC
date: 2017-08-15 10:20:07
tags:
  -
---

生产环境下，我们的 web server 往往并行的处理请求，各种请求的log交错的写到日志文件里。在并发量很大生产环境，搜索某个请求的Trace 如同大海捞针。

```
Request A's log: .....
Request B's log: .....
Request C's log: .....
Request A's log: .....
Request A's log: .....
Request B's log: .....
Request A's log: .....
Request B's log: .....
Request C's log: .....
Request C's log: .....
Request B's log: .....
Request C's log: .....
Request A's log: .....
Request B's log: .....
Request C's log: .....
```


如果 Rails 能为每个Request 创建一个 uuid，并且在写log时，附上这个uuid就好了。就可以通过这种方法搜索出完整的 trace。


```shell
tac production.log | grep '7efb4d18-8e55-4d51-b31e-119f49f5a410'
```

Rails 还真的有这个功能。对于 Rails4，配置方法如下：


```
# Rails 4
# /config/environments/production.rb
Blog::Application.configure do
  config.log_tags = [:uuid]
end
```

对于 Rails5，配置方法如下：

```
# Rails 5
# /config/environments/production.rb
Blog::Application.configure do
  config.log_tags = [:request_id]
end
```

这样每一个 Request 的 uuid 就会附在 log 中。

![](/media/files/2018/2018-07-21-log.png)



## Reference

1. [Rails 5 supports logging errors with tagged logging](https://blog.bigbinary.com/2016/06/28/rails-5-supports-logging-errors-with-tagged-logging.html)

2. [Railscast: #56 The Logger (revised)](http://railscasts.com/episodes/56-the-logger-revised?view=asciicast)