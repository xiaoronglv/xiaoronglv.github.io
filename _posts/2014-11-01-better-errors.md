---
title: 在 Rails 中友好的展示错误信息
layout: post
guid: d2utkzh6a62j
date: 2014-11-01 22:33:24
tags:
   - Ruby
---

每天都有成千上万的新用户注册，有的来自中国大陆，有的是国际友人。他们注册网站时，可能会使用奇奇怪怪的用户名，含有空格、含有火星文、含有敏感词等等，导致注册失败。

用通俗易懂的语言给予反馈，是用户体验很重要的一环。

若是英文用户，我们希望提示英语「Name can't be blank」。

![英文提示]({{ site.url }}/media/files/2014/Nov/2014-11-01-en.png)


若是中文用户，我们希望提示中文「用户名不能为空」。

![中文提示]({{ site.url }}/media/files/2014/Nov/2014-11-01-zh-CN.jpg)

## 在 Rails 中如何实现呢？


假如在 Rails 中的用户模型有以下四个验证

``` ruby
class User < ActiveRecord::Base
  # 验证1: email 不能为空
  validates :email, presence: true,

  # 验证2: email 必须唯一
  validates :email, uniqueness: true

  # 验证3: username  不能为空
  validates :username, presence: true

  # 验证4: username 必须唯一
  validates :username, uniqueness: true
end
```

验证没有通过，Rails 就会调用相应 key 中的 message.

``` ruby
activerecord.errors.models.user.attributes.email.blank

activerecord.errors.models.user.attributes.email.taken

activerecord.errors.models.user.attributes.username.blank

activerecord.errors.models.user.attributes.email.taken
```

其实所谓国际化，就是 Rails 在查找错误信息时，若是英文用户，就给一把英文 key 的 message，若是中文用户，就给一把中文 key 的 message。

实现错误信息的国际化其实相当简单，只要在 appname/config/locales 中添加两个 I18 文件就成了。

![中文国际化文件]({{ site.url }}/media/files/2014/Nov/2014-11-01-zh-CN-file.png)

![英文国际化文件]({{ site.url }}/media/files/2014/Nov/2014-11-01-en-file.png)

``` ruby
u = User.new
u.valid?
u.errors.full_messages
=> ["用户名不能为空", "Email 不能为空"]
```

你看，这事就成了。中文用户注册出错时，错误信息就是中文的。
