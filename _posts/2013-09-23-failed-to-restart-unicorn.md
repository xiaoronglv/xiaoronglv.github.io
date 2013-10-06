---
title: 权限错误导致 unicorn 重启失败
layout: post
guid: kNUmGivdUtgw1
tags:
  - Ruby
---
<pre><code>
deployer@server:~$ /etc/init.d/unicorn_myapp start
-bash: /etc/init.d/unicorn_myapp: Permission denied
</code></pre>

前段时间使用capistrano部署Rails时，经常无法重启unicorn。排查原因时发现是unicorn_init.sh的权限错误，导致unicorn无法执行。

感谢[Stack Overflow](http://serverfault.com/questions/415818/permission-denied-when-starting-unicorn-on-ubuntu)，我找到了一个折中的办法：

1. 每次部署后修改这个文件的权限；
2. 然后手工重启unicorn。

<pre><code>
chmod a+x /etc/init.d/unicorn_myapp
sudo /etc/init.d/unicorn_myapp restart
</code></pre>

但每次部署都这么痛苦，不是长久之计。后来Sam说：你好傻，为什么不在本地的代码仓库中修改unicorn_init.sh的权限，然后再发布。

## 小结

如果你的server因为权限问题无法重启unicorn，你可以尝试在本地修改unicorn_inti.sh权限，然后再发布。

<pre><code>
chmod a+x unicorn_init.sh
</code></pre>
