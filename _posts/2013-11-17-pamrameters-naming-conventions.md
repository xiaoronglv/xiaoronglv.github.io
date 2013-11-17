---
title: Rails parameter 命名习惯
layout: post
guid: vaoz0fezuTcC1
tags:
  - 
---

<span class="image-800">![Nested Model Form]({{ site.url }}/media/files/2013/nov/14-1.jpg.png)</span>


## 1 场景

标签（label）与广告（ad）是典型的多对多的关系。

Label Class
<pre><code>
has_many :label_ads
has_many :ads, through: :label_ads
</code></pre>

Ad Class
<pre><code>
has_many :label_ads
has_many :labels, through: :label_ads
</code></pre>

LabelAd Class
<pre><code>
belongs_to :label
belongs_to :ad
</code></pre>

需求是：编辑label时，可以新增或删除label与ad的关系。


## 2 如何解决


### 2.1 第一种尝试

动手前先拜一拜google大神，发现 Ryan Bates 在 Railscast 讲解过类似的场景。为了尽快上线，我决定使用 Rails框架自带的功能 [Nested Model Form](http://railscasts.com/episodes/196-nested-model-form-revised)

在做的过程中，越来越发现 Rails Nested Model form 并不能完全满足我的需求，而此时距离 deadline 只有一天了，心里开始慌张了。

期间 Vincent 和 zgm 一直劝我，不要拘泥于 Rails 的框架，框架之外的世界很精彩。

好羞愧，我现在只是个Rails程序员，不是Ruby程序员，也不是Web程序员。我是个睁眼瞎，看不到外面的世界。


### 2.2 第二种次尝试

2天的瞎折腾后，得出一个结论：在View层裸写表单才是唯一出路。

要想用form_tag 裸写表单，Rails parameter 的命名习惯一定要熟烂于心。


#### 左→右，key依次递进

<pre><code>
<%= form_tag articles_path, method: :post do %>

  <%= text_field_tag "apple[1][a]" %> 
  <%= text_field_tag "apple[2]" %>
  <%= text_field_tag "apple[3]" %>

<%=  submit_tag %>
<% end -%>
</code></pre>

Log中提交的参数：

<pre><code>
Parameters: {"utf8"=>"✓",
"authenticity_token"=>"iqJ4evgluTmJhVJzqkGHxkEL2gV2U8NbcJSPT3ZZiH8=", "apple"=>{"1"=>{"a"=>"X"}, "2"=>"Y", "3"=>"Z"},
"commit"=>"Save"}
 </code></pre>

在这个范例中，Rails是按照如下方法处理表单中的数据：

从左到右，依次为key。

#### 中括号[]的用途

假如表单中的两个字段name相同，后面的数据就会覆盖前面的数据，比如这段代码

1. new.html.erb
    <pre><code>
    <%= form_tag articles_path, method: :post do %>
      <%= text_field_tag "apple[1]" %> 
      <%= text_field_tag "apple[1]" %>
    <% end -%>
    </code></pre>


2. 提交表单后，log中的parameter
    <pre><code>
    {"utf8"=>"✓",
    "authenticity_token"=>"iqJ4evgluTmJhVJzqkGHxkEL2gV2U8NbcJSPT3ZZiH8=",
     "apple"=>{"1"=>"b"},
     "commit"=>"Save changes"}
     </code></pre>

    后面的参数把前面的覆盖了，所以只剩下`"1"=>"b"`



但假如在name后面加一个中括号，瞬间就「有容乃大」，变成一个数组了。

1. new.html.erb
    <pre><code>
    <%= form_tag articles_path, method: :post do %>
      <%= text_field_tag "apple[1][]" %> 
      <%= text_field_tag "apple[1][]" %>
    <% end -%>
    </code></pre>

2. 参数
    <pre><code>{"utf8"=>"✓","authenticity_token"=>"iqJ4evgluTmJhVJzqkGHxkEL2gV2U8NbcJSPT3ZZiH8=",
     "apple"=>{"1"=>["a",
 "b"]},"commit"=>"Save changes"}</code></pre>
 
 
## 参考资料

1. [Rails Guide: Understanding Parameter Naming Conventions](http://guides.rubyonrails.org/v2.3.11/form_helpers.html#understanding-parameter-naming-conventions)

2. [Railscast: Nested Model Form](http://railscasts.com/episodes/196-nested-model-form-revised)