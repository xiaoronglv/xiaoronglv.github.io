用测试来教育别人

几个月前我的好朋友在向我鼓吹 Elixir 的哲学时，顺便提到了「显示调用」 —— 用直白的话告诉读者，做了 A/B/C/D/E 五件事情；而不是调用 A 时，偷偷的触发一个 callback，偷偷摸摸的做完剩下的事情。

"Callbacks are evil" - by Teddy.


比如以下几种资源都可以被标记标签。当删除这些资源时，要重新计算某个公司标签的统计信息。该如何维护资源和标签之间的一致性？

```
class Photo
	include Taggable

end
```


```
class Video
  include Taggable
  
end
```


## Teddy 的策略一：你们要把自己的屁股擦干净！

Teddy 坚持认为，优秀的程序员要为整体架构负责，要有大局观。既然你要使用 taggable 这个模块，就要肩负起擦屁股(dependents) 的责任，把一致性维护好。

```
class VideosController
  def destroy
    @video.soft_delete
    @video.process_tags # 显式擦屁股
    head :no_content
  end
end
```

我其实很赞成他的观点，但是不得不处理几种窘境。

林子大了，什么鸟都有。总会有一些不负责任的程序员。他们并不在意整体概念的完整性，数据不一致？统计信息出错了？他们不关心，他们只盯着自己的一亩三分地，完成任务就万事大吉。

此外还有大票的人，他们很负责任，很有才华，但是初次接触20万行的项目，能力上无法「顾全大局」。也可能无意间会漏掉擦屁股的操作。

种种因素加起来，使的该策略的执行性很差。


## 策略二：模块作者用 callback 帮大家擦屁股

于是很多模块的开发者只能肩负起擦屁股的责任，当某个资源的状态发生变化时，自动触发 callback 来维护一致性。

```
module Taggable
  def self.included(base)
    base.class_eval do
      after_save :process_tags
    end
  end

  def process_tags
    # soft delete
    # reconcile stats system
    # ...
    # ...
  end
end
```

调接口的人省掉了一行代码，更"简洁"了，他也更开心了。

```
class VideosController
  def destroy
    @video.soft_delete
    # @video.process_tags 
    head :no_content
  end
end
```

省事真的是好事吗？

如果你引入的 10 个 module 里面藏着 10 个 callbacks，帮你做了一堆的事情。你真的开心吗？

1. 他们藏在哪？
2. 他们有顺序依赖吗？
3. 里面有炸弹吗？会不会再次触发几十个 callback ？
4. 他们里面有依赖外部服务的 callback 吗？会不会让数据库 transaction 一直无法 commit？


维护一致性的责任变成了皮球，被踢来踢去。大家各执一词，谁都有道理，谁都不是最好方案。就我个人而言，我偏向于第一种策略，但是苦于可执行性很差。在一次头脑风暴中，Eric Wang 说：我们可以用 test case 来教育用户吗？

1. 仍坚持显式调用（策略一）
2. 如果调用者忘记擦屁股，就一次次的挂测试，并且告诉他具体原因，"教育"他！
3. 最终结果
  1. 调用者可以选择修改自己的代码以符合规范；
  2. 或声明他是个[idiot](https://github.com/seattlerb/minitest/blame/master/lib/minitest/test.rb#L24)；
  3. 或把自己添加到白名单里，让他 sign off 。


## Case 1

helper

module BB
  @@bases = []
  def self.included(base)
    @@bases << base
  end
end


如果某种 convention 是美的，令人信服的，毫无争议的，代表了团队的品位，也可以用这种策略来保证"规矩"。



---

Thanks to Teddy Wang for reading drafts of this.
