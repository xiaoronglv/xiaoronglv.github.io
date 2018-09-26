---
title: UltiSnips 让 Vim 飞起来
layout: post
guid: 9fc2a2d3266f4cdca88e0c20fcbb929c
date: 2015-04-02 21:07:07
tags:
  - Vim
---

![datetime](/media/files/2015/2015-04-06-datetime.gif)

Sublime Text 中的代码块 (Snippets) 缩减了码字的过程，极大的提高编程效率，是非常方便的工具。当我把编辑器迁移到 Vim 后就开始寻觅一款适合自己的 Snippet 插件，以下是我折腾 Vim 代码块的经验总结。

初学 Vim，还有很多不懂的地方，若有纰漏，请及时指出。

Vim 比较流行的代码块引擎有 SnipMate 和 UltiSnips。目前越来越多的人选用 UltiSnips，它的优点有：

* 可以在代码片段中中嵌入 python / vim / bash  代码，动态实现一些很酷功能 (后文有演示 demo)
* 兼容 SnipMate
* 支持正则
* (据说) 响应速度更快，性能更好一些

UltiSnips 只是个引擎，需要搭配预设的代码块才能运转起来，以下是我创建的几个常用代码块。

```
# 当我在编辑器里输入 `test + tab`， 会自动补全以下代码
# test 'method name' do
#
# end

snippet "test" "test a method" r
$0
endsnippet


# 当我在编辑器里输入 `defs + tab`，会自动补全以下代码
# def self.method(*args)
#
# end

snippet "defs" "define a class method" r

def self.method${1:(${2:*args})}
  $0
end
endsnippet

```

在创建代码块的过程中我遇到很多问题， 如 trigger 命名不合理，Tab 的跳转不高效等。我特别想借鉴别人撰写 snippet 的经验。这个世界上总是有一批又懒又聪明的程序员，我在 Github 中轻松搜到一个比较好的代码块合集，叫 [honza/vim-snippets](https://github.com/honza/vim-snippets)，几乎涵盖所有编程语言。

与其自己一个个创建代码块，不如先站在巨人的肩上，然后再慢慢折腾，于是乎我的折腾思路调整为

1. 安装引擎 SirVer/ultisnips
2. 安装代码块集合 honza/vim-snippets
3. 添加自定义代码块，把优先级调高，覆盖掉 honza/vim-snippets 中不满意的代码片段。

## step 1 安装 SirVer/ultisnips

使用 Vundle 管理 vim 插件，安装插件十分简便，在 .vimrc 中引入一行代码即可。

```
Plugin 'SirVer/ultisnips'
```

### Step 2 安装代码块集合

```
Plugin 'honza/vim-snippets'
```

安装完后就可以在 Vim 中使用代码块了，以下是几个经常用到的代码片段 demo ，是不是很酷？


1. 插入当前日期 (trigger: date)

    ![date](/media/files/2015/2015-04-06-date.gif)

2. 插入当前时间(trigger: datetime)

    ![datetime](/media/files/2015/2015-04-06-datetime.gif)

3. 插入一个大的注释框 (trigger: bbox)

    ![bbox](/media/files/2015/2015-04-06-bbox.gif)

4. 在 erb 中插入 if 语句

    ![ife](/media/files/2015/2015-04-06-ife.gif)


##  Step 3 创建自定义代码块


我们需要定制代码块来满足特殊需求，比如我希望在 rb 文件中输入  `atc + tab` 时可以快速的生成一个 ActiveSupport::Testcase，可以按照以下步骤实现这个功能。

### 3.1 在 ~/.vim 下创建文件夹，命名为 UltiSnips，用来存放自定义的代码块。

> 有的同学会直接修改 `~/.vim/bundle/vim-snippets` 中的 snippets，建议不要这个干！因为这个目录是 Git 仓库，每次重装插件会丢失未提交的文件。
>
> 还是单独创建一个 UltiSnips 文件夹来保存自定义代码片段比较稳妥。

```bash
# UltiSnips 插件会自动加载这个文件夹下的文件
mkdir ~/.vim/UltiSnips
```

### 3.2 创建 ruby.snippets

```
touch ~/.vim/UltiSnips/ruby.snippets
```

代码块的文件命名是有讲究的，ruby.snippets 中定义的代码块只会在 rb 文件中生效；python.snippets 中定义的代码块只会在 py 文件中生效。

假如你想定义一个通用的代码块，在所有的文件类型中都能用，你可以把它定义在 all.snippets 中。

```
# ~/.vim/UltiSnips/ruby.snippets

snippet atc "test case of ActiveSupport::TestCase" !b
require 'test_helper'

class ${1:klass}Test < ActiveSupport::TestCase
   test '${2:case_name}' do
    ${0}
   end
end
endsnippet

```

![atc](/media/files/2015/2015-04-06-atc.gif)


### 3.3 再举个 markdown 的例子

```
# when I type head + tab, I hope...
---
title: title
layout: post
guid: e1eff5aaf0db41198c0f4acfc5ba4a9c
date: 2015-04-02 21:45:31
tags:
  -
---
```

假如你用 jekyll 写博客，肯定对上面这段代码很熟。这是每篇博文头部的 head 信息
，输入十分繁琐。

我特别希望在 markdown 文件中输入 `head + tab` 时可以自动生成博客的头部。 自定义一个 markdown.snippets 就可解决这个问题。

```
# ~/.vim/UltiSnips/markdown.snippets

snippet head "Jekyll post header" b
---
title: ${1:title}
layout: post
guid: `!p
import uuid
if not snip.c:
  guid = uuid.uuid4().get_hex()
snip.rv = guid
`
date: `!v strftime("%Y-%m-%d %H:%M:%S")`
tags:
  - ${4}
---

${0}
endsnippet
# vim:ft=snippets:
```

这段代码中 guid 是用一段 Python 脚本生成的， date 是用 Vim 脚本生成的，`${0}` 代表 tab 最终停留的位置， `b` 代表 begin (snippet should be expanded only at the beginning of a line)。

![head](/media/files/2015/2015-04-06-head.gif)

It's cool!


## 总结

有好多 Vim er 高手不用代码块 (snippet) ， 他们手工撸制每一行代码，喜欢原汁原味。每个人的偏好不同，使用 Snippet 真的极大的提高了工作效率。


## 参考资料

1. [xiaoronglv's vim config](https://github.com/xiaoronglv/dotfiles)
2. [Why UltiSnips?](http://fueledbylemons.com/blog/2011/07/27/why-ultisnips/#options)
3. [Github: UltiSnips Source code](https://github.com/SirVer/ultisnips)
4. [UltiSnips Mannual](https://github.com/SirVer/ultisnips/blob/master/doc/UltiSnips.txt)


