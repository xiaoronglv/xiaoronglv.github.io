---
title: 最干净的 select
layout: post
guid: NnLpCiVXg9kf
tags:
  - Rails
---

Rails form 中的 select，怎么写才够优雅？

## 第一种境界

在form中，以数组的方式列出select的值
<pre><code>
<%= simple_form_for @user do |f| %>
  <%= f.input :sex, collection: ['male','female'] %>
<% end %>  
</code></pre>


缺点：

假如日后增加一种性别 '女博士' ，需要修改相应的表单。一个表单还好办，假如有A、B、C三个表单调用，改起来不是一般的麻烦。

1. 违反了DRY原则，
2. 不好维护。




##　第二种境界

把性别写到 model 的常量中，在表单中调用。如果要增加一种性别，直接修改类常量。
<pre><code>
class student
  SEX = ['Male', 'Female']
  attr_accessor :sex, :name, :age
end
</code></pre>
<pre><code>
<%= simple_form_for @user do |f| %>
  <%= f.input :sex, collection: Student::SEX%>
<% end %>  
</code></pre>

这种代码看起来稍具有可读性，也便于维护，但是还不够间接。 



##　第三种境界


最干净，最简洁，最容易维护

<pre><code>
<%= simple_form_for @user do |f| %>
  <%= f.input :sex %>
<% end %> 
</code></pre>

如果想要radio_button 的样式，可以写为

<pre><code>
<%= simple_form_for @user do |f| %>
  <%= f.input :sex, :as => :radio_buttons %>
<% end %>
</code></pre>
 
为什么不需要写 collection 呢？

因为项目里使用了enumerize这个gem来实现I18n，它恰巧可以搭配 simple_form，自动判断input的类型。 