---
title: validates_presence_of 不适用于 boolean 字段
layout: post
guid: t7PaQAbl6whi
tags:
  - Ruby
  - Rails
---


validates_presence_of 广泛的应用于 Rails model 的验证，但它不能应用于 boolean 字段。因为 false 在 validates_presence_of 眼中，是 invalid，无法通过验证！

## 范例 

1. 定义 confirmed 字段为 boolean，并添加 validates_presence_of 验证。

    <pre><code>
    Class Evaluation
         ...
         validates :confirmed, presence: {message: "请填写是否通过微信验证？”}
         ...
    end
    </code></pre>

2. 使用 Rails Console 新建一个 Evaluation 对象，confirmd 字段的值设为 false

    <pre><code>
      [1] pry(main)> a = Evaluation.new(:confirmed => false)
      => #<Evaluation id: nil, 
                      sex: "female",  
                      confirmed : false>
    </code></pre>


3. 验证

    confirmed 的值为 fasle（不为空），但是依然无法通过 validates_presence_of 验证。

    <pre><code>
    [2] pry(main)> a.valid?
    => false
    [3] pry(main)> a.errors[:confirmed ]
    => [“请填写该用户是否通过了微信验证"]    
    </code></pre>

    

## 官方文档

查看 validates_presence_of 的官方文档可以发现这段声明：

> If you want to validate the presence of a boolean field (where the real values are true and false), you will want to use validates_inclusion_of :field_name, :in => [true, false].

验证 boolean 字段，我们应该使用 validates_inclusion_of，一定要谨记哦！

<pre><code>
Class Evaluation
  ...
  # 正确的写法1
  validates :confirmed, inclusion: {in: [true, false], message: "请填写是否通过微信验证？"}

  # 正确的写法2
  validates_inclusion_of :confirmed, in: [true, false], message: "请填写是否通过微信验证？"
  ...
end
</code></pre>


## 参考资料

1. [API doc: validates_presence_of](http://apidock.com/rails/ActiveRecord/Validations/ClassMethods/validates_presence_of)
2. [API doc: validates_inclusion_of](http://apidock.com/rails/v2.3.8/ActiveRecord/Validations/ClassMethods/validates_inclusion_of)
3. [Stackoverflow: Why does false invalidate validates_presence_of?](http://stackoverflow.com/questions/2883823/why-does-false-invalidate-validates-presence-of)