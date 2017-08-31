## 为什么需要它

多个服务器的log搅和在一起。

根本没有办法看。


## 如何设置？

Ryan Bates 的官方文档。


## 人们可以用来干什么？

Stack Overflow 的那个帖子



## Rails 是什么时候干这些事情的？


## 1. How to setup log tags in Rails?

1.1 setup log_tag in application.rb. log tag can be a string, :symbol, block.  For ct, it's a lambda.

config.log_tags = [lambda {|request| "ct: #{request.uuid[0..7]}"}]


1.2 When rails web server are started, this lambda will be configured in `::Rails::Rack::Logger` as an instance variable `@taggers`.

```
module Rails
  class Application
    class DefaultMiddlewareStack

          ...
          middleware.use ::Rack::Runtime
          middleware.use ::Rack::MethodOverride unless config.api_only
          middleware.use ::ActionDispatch::RequestId
          middleware.use ::ActionDispatch::RemoteIp, config.action_dispatch.ip_spoofing_check, config.action_dispatch.trusted_proxies

          middleware.use ::Rails::Rack::Logger, config.log_tags
          middleware.use ::ActionDispatch::ShowExceptions, show_exceptions_app
          middleware.use ::ActionDispatch::DebugExceptions, app, config.debug_exception_response_format
          ...
    end
  end
end
```


## 2 the flow of middlewares

the following rails middleware stacks is borrowed from Rails documentation

```
use Rack::Sendfile
use ActionDispatch::Static
use ActionDispatch::Executor
use ActiveSupport::Cache::Strategy::LocalCache::Middleware
use Rack::Runtime
use Rack::MethodOverride
use ActionDispatch::RequestId
use ActionDispatch::RemoteIp
use Sprockets::Rails::QuietAssets
use Rails::Rack::Logger
use ActionDispatch::ShowExceptions
use WebConsole::Middleware
use ActionDispatch::DebugExceptions
use ActionDispatch::Reloader
use ActionDispatch::Callbacks
use ActiveRecord::Migration::CheckPending
use ActionDispatch::Cookies
use ActionDispatch::Session::CookieStore
use ActionDispatch::Flash
use Rack::Head
use Rack::ConditionalGet
use Rack::ETag
run MyApp.application.routes
```

`ActionDispatch::RequestId` is for generating uuid for each request.

`Rails::Rack::Logger` is for appending this uuid to each line of log.



https://github.com/rails/rails/blob/master/railties/lib/rails/rack/logger.rb#L17

```
module Rails
  module Rack
    class Logger < ActiveSupport::LogSubscriber
      def initialize(app, taggers = nil)
        @app          = app
        @taggers      = taggers || []
      end
      ...
    end
  end
end
```



## tagged 这个方法为什么可以影响到 block 中的 logger.info?

一个图，比较方法的变化。



## 3. How does `ActiveSupport::TaggedLogging.new` work?

e.g.

logger = ActiveSupport::TaggedLogging.new(Syslogger.new('rails-ct-stage'))
logger.tagged('BCX', "Jason") { logger.info 'Stuff' }

> Output: Logs "[BCX] [Jason] Stuff"


3.1 What's going on under the hood?

```
#source code
def tagged(*tags)
  new_tags = push_tags(*tags)
  yield self
ensure
  pop_tags(new_tags.size)
end
```

Let's transform this fancy method to be more primitive.


3.2 remove push/pop tags

```
# pseudo code
def tagged(*tags)
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] << [tag1, tag2, tag3...]
  yield self
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] delete [tag1, tag2, tag3...]
end
```

3.3 remove yield

```
# pseudo code
def tagged(*tags)
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] << [tag1, tag2, tag3...]
  logger.info 'Ryan is testing'
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] delete [tag1, tag2, tag3...]
end
```

3.3 replace `logger.info` with its own source code

```
# pseudo code
def tagged(*tags)
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] << [tag1, tag2, tag3...]
  string = formatter.call(severity, timestamp, progname, msg)
  IO.write string
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] delete [tag1, tag2, tag3...]
end
```

3.4 remove formatter

```
# pseudo code
def tagged(*tags)
  tags = Thread.current["activesupport_tagged_logging_tags:#{object_id}"] << [tag1, tag2, tag3...]
  string = [severity, timestamp, progname, "#{tags}#{msg}"]
  IO.write string
  Thread.current["activesupport_tagged_logging_tags:#{object_id}"] delete [tag1, tag2, tag3...]
end
```


## 4. implementation for jam

1. formatter.call is the essential steps for `ActiveSupport::TaggedLogging.new`.

2. invoke formatter.call in syslogger.add

3. remove overlap parts between syslog-ng and Logger::Formatter, such as progname, time...

