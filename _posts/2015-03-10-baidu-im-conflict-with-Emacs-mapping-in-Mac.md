---
title: 百度输入法导致 Mac Os 系统的 Emacs 快捷键 Ctrl + p 失效
layout: post
guid: t72njjhpp9g7
date: 2015-03-10 18:07:05
tags:
   -
---


在使用 Sublime、TextEdit、iTerm 时，我习惯使用 Emacs 的组合键来移动光标，手指不用离开基本键位，效率很高（懒）。

`Ctrl` + `a` - move to start of current paragraph
`Ctrl` + `e` - move to end of current paragraph
`Ctrl` + `f` - move right one character ``→`
`Ctrl` + `b` - move left one character `←`
`Ctrl` + `n` - move down one line `↓`
`Ctrl` + `p` - move up one line `↑`


但是如果 Mac 上安装了百度输入法时，会导致 Emacs 的快捷键 Ctrl+p 失效。在百度输入法中 ctrl + p 是切换全拼、双拼。


![BaiduIM bug]({{ site.url }}/media/files/2015/March/2015-03-10-baiduIM.png)

取消勾选既可解决这个冲突。
