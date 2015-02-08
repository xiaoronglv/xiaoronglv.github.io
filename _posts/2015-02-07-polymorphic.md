---
title: Rails STI 的两种使用场景
layout: post
guid: 52x19pwskhjb
date: 2015-02-08 16:24:34
tags:
   -
---

本文是我总结的 Rails 多态的两种使用场景

## 场景1： 多个类(Post/Photo/Article) 与某个类 (comment)的关系为 1:n

我们的业务中经常会碰到这种场景。

Post、Photo、Article 都可以被评论，他们与 Comment 的关系为

    Post(1) : Comment(n)  
    Photo(1) : Comment(n)  
    Article(1) : Comment(n)  


如果定义三个 Comment 类，会十分啰嗦。这时候就可以使用Polymorhpic 来优雅的实现。


```ruby
class Post
  has_many :comments, as: :commentable
end


class Article
  has_many :comments, as: :commentable
end


class Photo
  has_many :comments, as: :commentable
end


class Comment
  belongs_to :commentable, ploymorphic: true
end
```

后来和瓜哥、治民交流的时候，他们经常说，可以使用多态来替代 Single Table Inheritance (STI)。我总是难以理解， STI 和多态解决的是两种业务场景呀？

后来看了 Ryan Bates 在某一期 Railscast 中的讲解才豁然开朗，原来多态还可以这么玩。


## 场景2： 替代 Single Table Inheritance (STI)

我们可以使用 STI 可以把父类，子类的数据放到一张表里。但随着业务的膨胀，子类和父类的差别可能会越来越多。

所以，当我在 STI 和 多态之间犹豫不决的时候，会拿这个标准来衡量：如果能预见到未来不可避免的差异（超过三个字段），一开始就不选用 STI，而是选用多态。


这是 Railscast 中的一个范例，非常具有代表性。


```ruby
class User < ActiveRecord::Base
  has_many :tasks, dependent: :destroy
  belongs_to :profile, polymorphic: true
end


class GuestProfile < ActiveRecord::Base
  has_one :user, as: :profile, dependent: :destroy
end


class MemberProfile < ActiveRecord::Base
  has_one :user, as: :profile, dependent: :destroy
end
```

![Polymorphic]({{ site.url }}/media/files/2015/Feb/2015-02-08-polymorphic.png)

## 参考资料

[Railscast: STI and Polymorphic Associations](http://railscasts.com/episodes/394-sti-and-polymorphic-associations)




