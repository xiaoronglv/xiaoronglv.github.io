---
title: 无力感
layout: post
guid: 6ekRp0ZDCwVGuAj
date: 2016-01-01 00:14:13
tags:
  - Ruby
---

本文是「参与大项目的酸甜苦辣」成长系列的一部分。

大项目之 [吐槽架构](/architecture.html)    
大项目之 [如何提高对烂代码理解速度？](/what's-the-fuck-I-am-reading.html)    
大项目之 [如何应对无力感？](/nothing-happens.html)  

---

我参与一个大项目8个月了，最近有点沮丧，事情是这个样子的。

Rails App 缓存策略粗分为五层[1]，效率从高到低依次为：

1. HTTP Cache
2. Page cache
3. Action cache
4. Fragment Cache
5. Rails.cache

越靠近客户端，缓存的效率越高。

感谢 Vincent Xie 给我一个几千万用户的平台做实验室，让我对各种缓存的应用场景得心应手。因此我想应用到目前从事的大项目上。考虑到既往的经验都是建立在简洁项目上[2]，为了稳妥，针对 HTTP Cache 我还是做了大量的准备工作以应对复杂的场景。

* 通读 RFC 7232 HTTP/1.1 [Conditional Requests](http://tools.ietf.org/html/rfc7232#page-9)
* 阅读 [Rack::ConditionalGet](https://github.com/rack/rack/blob/master/lib/rack/conditional_get.rb) 和 [Rack::ETag](https://github.com/rack/rack/blob/master/lib/rack/etag.rb) 的源码
* 阅读 Rails [fresh_when/stale?](https://github.com/rails/rails/blob/master/actionpack/lib/action_controller/metal/conditional_get.rb) 的源码


晚上我梦到 Response time 从 300ms 降至 40ms；性能监控平台的曲线像瀑布一样美；数据库请求数减至一半；剩下的 SQL 查询变为 index-covered query；没有读取 Memcached 的时间；日志华丽的显示 `Views: 2ms | ActiveRecord: 2.8ms`。

快乐、满足、持久的幸福感等一切美好的情感涌向心头。喝杯咖啡，然后继续调优下一个 most visited page。

恩...梦醒了，事情好像没有想象的那简单。

**首先，URL中有很多变化因素**

同一个 URL 请求，有的是 pjax，有的完整页面请求，并且移动端还有特殊适配，该如何处理？

花几天的时间想策略，无果。[3]

**其次，layout 中有大量的逻辑代码**

花费几个小时钻研代码，没看懂。

**第三，导航条的逻辑非常复杂，牵涉到大量的判断和鉴权**

花费几个小时钻研代码，因业务不熟悉，没看懂。

**第四，表结构为什么要这样设计**

反范式，还是草率的决策?

挖坟无果，不得而知。

**第五，内部的性能监测工具无法满足需求**

how to measure and profile？

尝试去阅读性能监控平台的代码，发现这个工具的数据库是 [HANA](http://hana.sap.com/abouthana.html)。不太熟悉，也不想折腾闭源的东西。而且完全看不懂 New Relic 的 Ruby Agent，所以没有资格去评论别人的工具如何如何。

**最后，还要咨询运维的同学**

反向代理的版本？开启 gzip 后会自作聪明的重新计算 ETag 吗？

此时，热情已消耗殆尽。

曾经改几行代码，就可以在 New Relic 上愉快的看数据。而如今却牵扯到如此多(且不擅长)的因素，不再是几行代码就可以搞定。

```ruby
def index
  @wikis = Wiki.where(deleted: false)
  fresh_when last_modified: @wikis.maximum(:updated_at)
end

def show
  @wiki = Wiki.find params[:id]
  fresh_when @wiki
end
```

综使我如此喜爱，纵使如此努力，纵使如此细心的准备，又有什么用呢？

Nothing happens. 没有瀑布的一泻千里，没有快如闪电的服务器响应，留在心头的只有看不懂的业务代码和深深的挫败感。我真的很忧虑这种生活成为常态，我该怎么办？

**为了自己的'宏图大计'慢慢熬？**

一点点的啃，一点点的改，花时间去熟悉业务逻辑，猜测各种奇幻代码的神秘意图，说不定一两年之后会对系统了如指掌。但没有乐趣，投入产出比低，说不定那时候一切也是惘然——业务代码看懂了，因历史原因仍做不了什么。

**亦或，老老实实的做一颗螺丝钉？**

公司付了薪水，我就用专业的态度把事情做好。但我只做一颗小小的螺丝钉，不操闲心，不期望从项目代码中获得幸福感。转头从开源项目找乐子，汲取知识。这种消极的工作态度令我十分厌恶，与其这样，为什么不再找一份更有乐趣的工作呢。

有时我会很乐观的想象：化冗杂为简洁，化腐朽为神奇，化无趣为乐趣或许是成长为架构师的必经之路。只有长不大的孩子才会沉浸在简单的小项目里。

比如这个正弦曲线，以原点为中心，如此对称，没有任何的移位。看似简洁完美(simple and perfact)，实则是因为简单(easy)，它忽略了公式中所有的参数。

y=Asin(ωx+φ)+k

![Easy](/media/files/2016/2016-01-01-sine-simple.gif)

当把各个参数补齐以后，曲线飘离原点，不对称。虽然难看，但是恢复了它的真实。

![Complicated](/media/files/2016/2016-01-01-sine-with-arguments.png)

1. 并不是所有的公司都热衷开源。在闭源工具上浅薄的使用经验对未来的成长没有任何价值，除非造轮子的工匠。

2. 垃圾代码在代码库中占大多数。猜测它们的意图很花时间，猜错了很生气，猜对了更生气。[4]

3. 写代码可以进入心流，但不是常态。
    * 业务复杂，不断被卡住，无法专注
	* 很多决策无法掌控，需要要考虑诸多因素
	* 无法立即得到反馈

4. 并不是所有的公司都使用 New Relic/Slack/Google App 等很爽的工具。

是沉浸在简单中，还是努力面对复杂的真实世界？我不知道答案，一年之后再来回答这个问题。[5]


## Notes

1.  [Heroku: Caching Strategies for Rails](https://devcenter.heroku.com/articles/caching-strategies)

2.  简洁 != 简单

	简洁：现实世界中存在的问题很复杂，但是作者的解决方式却有很高的扩展性、可维护性、美感。

	简单：现实世界中存在问题很简单，所以作者解决方式也很简单。

3. [Generate Controller-Wide ETags](http://blog.remarkablelabs.com/2012/12/generate-controller-wide-etags-rails-4-countdown-to-2013) 似乎也无法满足我的需求。

4. 大部分人无意制造垃圾，但是受限于技能，却在一直制造垃圾。

5. 神把我放到这个位置，自有他的美意。

感谢老板[吴坚](https://ruby-china.org/gene_wu)的包容，我可以坦然的说出自己的困惑。希望这篇文章能够帮助到有同样困惑的程序员。
