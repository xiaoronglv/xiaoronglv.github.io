---
title: 不要过分暴露变量
layout: post
guid: 3KuBjeEsmz3d
tags:
  - Rails
---

相信有不少人也和我一样，这样写 destroy action.

<pre><code>
# app/controllers/goods_controller.rb
def destroy
  @goods = Goods.find(params[:id])
  @goods.destroy
  redirect_to admin_goods_index_url
end
</code></pre>

这个对象删除后直接调转到新页面了。view 根本用不到 @goods ，用一个局部变量足矣。

这是第二次犯这种错误了，以后要常常提醒自己

1. 不要过分暴露变量的作用域，
2. view 中用不到的变量统统使用局部变量。