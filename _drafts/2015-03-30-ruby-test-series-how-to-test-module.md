---
title: Ruby Test: How to test module?
layout: post
guid: 94407771056c4323bafcc7023cac4a5f
date: 2015-03-30 08:15:57
tags:
  - Ruby
---

Rails 很早就引入了 Concern 层，存放一些跨类的 Module，如何为这些 module 写测试是使用 Rails 的同学共同面对的问题，大家的写法也各有千秋。

## 真实场景

```
# Module
module Printable
  def print_pdf
    "I will be exported to be a pdf file"
  end
end

# Word need to convert to pdf, so let mixin the module
class Word
  include Printable
end
```

Printable 被 Class Word 引入，如何为这个 Module 写测试呢？

## 测试思路1：为 Word 的实例方法写测试

既然 Word 已经 mixin Printable, Word 必然存在 `print_pdf` 这个实例方法，只需要在 Word 的 test case 中再添加一个 test 既可。

```
require 'test_helper'

class WordTest < ActiveSupport::TestCase
  #...
  #...

  # test print to pdf method
  # use factory girl to setup data
  test "print pdf" do
    word = create :word
    assert_equal @document.print_pdf, "I will be exported to be a pdf file"
  end
end
```

估计大部分人会选用这种测试思路，但是问题也很明显:

1. Printable 被多个类 mixin 时，无法在「DRY」和 覆盖率之间取舍。

    假如 Class Txt 也引入了 Printable，你还要继续在 `TxtTest` 中补一个测试吗？

2. 不够 Unit

    我们的测试意图是在 Printable, 但是却间接的通过 Word 去测试。两者揉合在一起，不像是一个 Unit。

3. 重构代码时增加了工作量

    `print_pdf` 本身并不是 Word 中定义的方法，在 code review 时 reviewer 可能会犯迷糊: 怎么莫名其妙出现了这个方法，是继承的？还是 mixin？我要去哪个 Module 中去找这个方法？


## 测试思路2: just write test for module

```
require 'test_helper'

class PrintableTest < ActiveSupport::TestCase
  def setup
    @document = Object.new
    @document.extend Printable
  end
  #...
  #...

  # test print to pdf method
  test "print pdf" do
    assert_equal @document.print_pdf, "I will be exported to be a pdf file"
  end
end
```

这是我在《The Minitest Cookbook》中看到的另外一种测试写作思路。先创建一个对象，然后 extend Printable，然后对这个对象写测试。

这里用到了元编程的一点技巧。当一个对象 extend Printable 时，其实打开了它的 eigenclass, 并且 include 这个模块。这个对象就有了 `print_pdf` 这个方法，可以十分干净的写一个测试。

这样写测试的好处: Module 的测试非常纯粹，不依赖其他类，像一个真正的 unit。review 代码时看起来比较清爽，不用被绕晕。

我很喜欢写测试，如果能把测试代码组织好，力争间接、DRY、更语义化，写测试也是一件十分有趣的事情。
