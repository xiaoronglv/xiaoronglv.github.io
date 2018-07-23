---
title: 如何为私有方法写测试
layout: post
guid: WbX7xchpKCQU
tags:
  - Rails
---

今天第一次给 Rails 的私有方法写测试，查阅了些资料，目前比较流行的写法有两种。

```
class MyClass
  private

  def foo
    true
  end
  
  def bar
    true
   end
end
```

## 方法1：修改私有方法为 public

重新打开这个类，修改私有方法为 public。

```
class MyClass
  public :foo
end

class GoodsOrderTest < ActiveSupport::TestCase
 ...
 ...
end
```

## 方法2：使用 send

```
test "should foo" do
  instance = MyClass.new
  instance.send(:foo)
end
```

推荐第二种方法。


## 参考资料

[Stackoverflow: What's the best way to unit test protected & private methods in Ruby?](http://stackoverflow.com/questions/267237/whats-the-best-way-to-unit-test-protected-private-methods-in-ruby)
