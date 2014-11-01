---
title: 在 Rails 中友好的展示错误信息
layout: post
guid: d2utkzh6a62j
date: 2014-11-01 22:33:24
tags:
   - Ruby
---

每天都有成千上万的新用户注册，有的来自中国大陆，有的是国际友人。他们注册网站时，可能会使用奇奇怪怪的用户名，含有空格？含有火星文？含有敏感词？等等...， 导致注册失败。

用户在使用我们的程序出错时，用通俗易懂的语言提醒Ta，是用户体验很重要的一环。

对于英文用户，我们当然希望提示英语提示「Name can't be blank」。

![英文提示]({{ site.url }}/media/files/2014/Nov/2014-11-01-en.png)


对于中文用户，我们希望提示中文「请填写您的姓名」。

![英文提示]({{ site.url }}/media/files/2014/Nov/2014-11-01-zh-CN.jpg)

## 在 Rails 中如何实现呢？


假如用户注册时，有以下四个验证

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

其实所谓国际化，就是 Rails 在查找错误信息时，若用户是英文用户，就给一把英文 key 的 message，若是中文用户，就给一把中文 key 的 message。

为了完成国际化，需要在 appname/config/locales 中添加两个 I18 文件就成了。

![英文提示]({{ site.url }}/media/files/2014/Nov/2014-11-01-zh-CN-file.png)

![英文提示]({{ site.url }}/media/files/2014/Nov/2014-11-01-en-file.png)

``` ruby
u = User.new
u.valid?
u.errors.full_messages
=> ["用户名不能为空", "Email 不能为空"]
```

你看，若用户是中文用户，错误信息就自动提示中文。

