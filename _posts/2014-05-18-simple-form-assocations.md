---
title: 实现打标签功能
layout: post
guid: xbDPO8YN1jOa
date: 2014-05-18 22:00:00
tags:
   - Rails
   - Ruby
---

## 需求

编辑商品时，可以打上标签。

![Rails Labels]({{ site.url }}/media/files/2014/May/18-labels.png)

产品经理提一些前端需求时，我经常酷酷的讲：投入产出不成正比，不如以后再说吧。

诚实的讲，是我的前端水平很挫，想当然的以为实现该功能会耗在 Javascript 上。

结果这周新来的 Ruby 工程师三下五除二搞定了这个需求，一行 JS 代码也没写，这就是老练的程序员与菜鸟的差距啊。

他的实现方式也超级简单。

## Model 层


    class Label < ActiveRecord::Base
      has_many :product_labels
      has_many :products, through: :product_labels
    end
    
    class Product < ActiveRecord::Base
      has_many :product_labels
      has_many :labels, through: :product_labels
    end
    
    class ProductLabel < ActiveRecord::Base
      belongs_to :label
      belongs_to :product
    end


has_many 这个类宏会自动在 Product 创建很多方法。


    Product#labels <<

    Product#labels.delete

    Product#labels=

    Product#label_ids
    
    # 我从未在意过这个方法。用途可能是根据 ids 创建很多 ProductLabel 对象，这个需要阅读源码进一步印证。
    Product#label_ids=
    ...


##  表单

然后最囧的一幕发生了，Nate 仅仅在 Product 的表单中加了一行代码就把这个需求做完了。 

products/_form.html.erb

    <%= simple_form_for(@product) do |f| %>
      ...
      ...
      # 你没看错，就是这行代码，囧
      <%= f.association :labels, as: :check_boxes %>
      ...
    <% end %>


## Simple Form Association

写到这里就不得不提 SimpleForm 的 Association.

The association helper just invokes `input` under the hood, so all options available to `:select`, `:radio_buttons` and `:check_boxes` are also available to association. Additionally, you can specify the collection by hand, all together with the prompt.


## 参考资料

[SimpleForm Association](https://github.com/plataformatec/simple_form#associations) 

[Railscast: SimpleForm](http://railscasts.com/episodes/234-simple-form-revised)

