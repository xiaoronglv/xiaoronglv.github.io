---
title: 开启 zsh 的 vi 模式
layout: post
guid: 3b91cd58369249aebfbcd10c0c5336b4
date: 2015-04-01 10:41:00
tags:
  -
---

为了装逼，我把编辑器从 Sublime 切换到了 Vim。现在不得不面临一个让人十分精神分裂的状况：Terminal 的快捷键是 Emacs 模式，编辑器是 Vim 模式。导航风格的迥异严重的影响了工作效率。

后来在装逼之王 [@zgm](https://ruby-china.org/zgm) 的指点下，给 zsh 开启了 vim 模式，既满足了装逼的乐趣，又提高了工作效率。

开启方式:

```
#.zshrc
# 只需要给 zsh 引入一个名叫  vi-mode 的 plugin 既可
plugins=(git vi-mode)
```

### 参考资料

1. [zsh plugin: vi-mode source code](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/vi-mode/vi-mode.plugin.zsh)
