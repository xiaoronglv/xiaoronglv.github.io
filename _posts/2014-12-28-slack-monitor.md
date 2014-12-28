---
title: 使用 Slack 和 exception_notification 监控 Rails 异常
layout: post
guid: 7d7v15sl4rdp
date: 2014-12-28 18:38:33
tags:
   -
---

每个程序员都希望 Sidekiq/Rails App 的抛异常时，能够尽快的被通知到，并且能附上整个堆栈、所有的参数。然后在最快的时间内处理掉异常。

exception_notification 这个 Gem 就是解决这类需求的一个好工具。

> The Exception Notification gem provides a set of notifiers for sending notifications when errors occur in a Rack/Rails application.

当程序出异常的时候，通过各种渠道第一时间通知程序员。

1. Email
2. Campfire
3. Hipchat
3. 其他自定义提醒方式


![邮件提醒]({{ site.url }}/media/files/2014/Dec/2014-12-28-sidekiq.jpg)

我以前偏爱邮件提醒，不过我刚刚发现了一个更好玩的，更及时的通知方式。


## Slack

Slack 是一个很棒的团队沟通工具，支持各种系统的接入比如 Google drive、Dropbox、 各种项目管理工具。

它是以 channel 来划分团队沟通通道，可以创建不同的 channel（有点像 QQ 群组）。

1. General: 全公司的人都在里面的频道
2. Random: 扯淡频道，各种八卦，各种段子
3. Ruby: Ruby 程序员讨论问题的地方
4. Android: 所有 Android 程序员讨论问题的地方
5. ...

Slack 提供了 webhooks，可以 post 数据至 webhooks，整个channel 的人都能收到。

利用这个特性，当我们的程序出异常时可通过 webhooks 向特定的 channel 发送消息，所有在那个频道的程序员都能立即收到消息。

以下就是实现这个混搭的过程：exception_notification + Slack

## 1. 创建一个 Sidekiq 的 Channel

![创建一个频道]({{ site.url }}/media/files/2014/Dec/2014-12-28-step1.jpg)

## 2. 查看这个 channel 的 webhooks

点此查看你的 [webhooks](https://my.slack.com/services/new/incoming-webhook)，其实就是一个 URL。

![查看 webhooks]({{ site.url }}/media/files/2014/Dec/2014-12-28-step2.jpg)


## 3. 引入 exception_notification 和 slack-notifier

```
# Gemfile
# Slack api 封装
gem 'slack-notifier'
# 异常监控
gem 'exception_notification'

```

## 4. 在 rails 中生成默认的配置文件 

```
rails g exception_notification:install
```

This command generates an initialize file (config/initializers/exception_notification.rb) where you can customize your configurations.

## 5. 在默认的配置文件中添加 slack notifier

```
# config/initializers/exception_notification.rb
require 'exception_notification/sidekiq'

# 自定义 notifier(slack)
module ExceptionNotifier
  class SlackNotifier

    attr_accessor :notifier

    def initialize(options)
      begin
        webhook_url = options.fetch(:webhook_url)
        @message_opts = options.fetch(:additional_parameters, {})
        @notifier = Slack::Notifier.new webhook_url, options
      rescue
        @notifier = nil
      end
    end

    def call(exception, options={})
      message = [
        "项目:  Plan",
        "Time: #{ Time.now.to_s }",
        "参数:  #{options.to_s}",
        "Backtrack: #{exception.backtrace.join("\n")}",
        "==============================================="
      ].join("\n\n")

      @notifier.ping(message, @message_opts) if valid?
    end

    protected

    def valid?
      !@notifier.nil?
    end
  end
end

# 换上你自己的 slack webhook 网址
ExceptionNotification.configure do |config|
  config.add_notifier :slack, {
    :webhook_url => "https://hooks.slack.com/services/your_webhooks",
    :channel     => "#sidekiq",
    :username => "plan"
  }
end
```

## 效果图

bingo! 搭建成功。

如果我们的程序抛异常了，就可以在 Slack 中第一时间内收到异常信息了。

![效果图]({{ site.url }}/media/files/2014/Dec/2014-12-28-slack.jpg)


## 参考资料

1. [Railscast: Exception Notifications](http://railscasts.com/episodes/104-exception-notifications-revised)

2. [exception_notification](https://github.com/smartinez87/exception_notification)

3. [slack-notifier](https://github.com/stevenosloan/slack-notifier)
