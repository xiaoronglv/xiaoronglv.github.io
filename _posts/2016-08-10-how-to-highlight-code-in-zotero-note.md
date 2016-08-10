---
title: 如何在 Zotero 中高亮代码
layout: post
guid: KpyFsbB9Q1yy
date: 2016-08-10 12:28:00
tags:
  - 
---

我的学习模式逐渐过渡为以主题为中心。每个月确定好几个感兴趣的主题，在不同的主题之间穿梭。

当我在学习 Elixir Programming 时，我希望进入 Elixir 的世界，一个贴满了便签纸软木板。各种概念、疑问、类比高高的挂在穹苍上。这些学习组块我可以在工作台，随手拿起放下任何组块，专注的加工[1]。

![](/media/files/2016-08-10-elixir.jpeg)


学累了 Elixir，当我切换到写作主题时，则又来到一个独立的软木板，贴满了和设计范式相关的组块。

![](/media/files/2016-08-10-writing.jpeg)

所以相应的，工具已经逐渐过渡到 Zotero\[2\](或Scrivener[3]) 。我的知识大多和编程相关，Zotero 的默认编辑器不支持语法高亮，十分影响我的学习效率。

开源软件的好处就是，界面丑陋，功能强大，随意定制。所以开始了折腾之旅。

首先我想到的是替换掉系统自带的丑陋的编辑器，但是没有插件支持，原因似乎是 Zotero 的开发团队为了软件稳定性，所以不鼓励这么干。


既然无法自定义编辑器，那么干脆用我更喜欢的 Markdown 来写笔记吧，顺便语法高亮。阳志平在这篇文章[4]中提到 Markdown Here 似乎是把 Note 中的 Markdown 转化为 Html，也就是说数据库中中保存的还是转换后的 Html，而不是最初的 Markdown 手稿。此外，也无法语法高亮。

就在山穷水尽之际，我不小心点开了编辑器的 `HTML` 去扫一眼笔记的 HTML 源代码。Note 居然保存了完整的 HTML 片段，不是 plain text！

也就是说，假如你从一个 Jekyll 网站复制一段代码，粘贴至 Note，它保存的是该 DOM 完整 HTML 源码。（以下是图解）

从网页复制一段代码

![](/media/files/2016-08-10-copy-to-zotero.jpeg)

拷贝到 Note，并查看 HTML 源码

![](/media/files/2016-08-10-check.jpg)

![](/media/files/2016-08-10-html-code.jpeg)


那么，是否可以通过自定义 Note 的 CSS 来实现代码高亮呢？

在 Zotero 的论坛找到一点线索[5]，我决定试试。

1. Preferences > Advanced > General > Open about:config
    ![](/media/files/2016-08-10-step1.jpeg)

2. 点击 I'll be careful, I promise!

    ![](/media/files/2016-08-10-step2.jpeg)

3. 搜索 `extensions.zotero.note.css`

    ![](/media/files/2016-08-10-step3.jpeg)

4. 把以下自定义的 CSS 塞到 extensions.zotero.note.css 的 value 中。

```css
pre {
    overflow-x: auto;
    padding: 10px 0;
    width: 100%;
    line-height: 1.2em;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
}
code {
    font-size: 13px;
    color: #eee;
    padding: 2px 4px 3px;
    border-radius: 2px;
    line-height: 1.3em;
    display: inline-block;
    vertical-align: baseline;
    zoom: 1;
    *display: inline;
    *vertical-align: auto;
}
:not(pre) > code {
    font-family: monospace;
    word-wrap: break-word;
    line-height: 1.2em;
    color: #003659;
    margin-top: 1px;
    margin-bottom: 1px;
    background: #d6eaf4;
    border-radius: 3px;
}


.highlight table td {
    padding: 5px;
}
.highlight table pre {
    margin: 0;
}
.highlight,
.highlight .w {
    color: #93a1a1;
    background-color: #002b36;
}
.highlight .err {
    color: #002b36;
    background-color: #dc322f;
}
.highlight .c,
.highlight .cd,
.highlight .cm,
.highlight .c1,
.highlight .cs {
    color: #657b83;
}
.highlight .cp {
    color: #b58900;
}
.highlight .nt {
    color: #b58900;
}
.highlight .o,
.highlight .ow {
    color: #93a1a1;
}
.highlight .p,
.highlight .pi {
    color: #93a1a1;
}
.highlight .gi {
    color: #859900;
}
.highlight .gd {
    color: #dc322f;
}
.highlight .gh {
    color: #268bd2;
    background-color: #002b36;
    font-weight: bold;
}
.highlight .k,
.highlight .kn,
.highlight .kp,
.highlight .kr,
.highlight .kv {
    color: #6c71c4;
}
.highlight .kc {
    color: #cb4b16;
}
.highlight .kt {
    color: #cb4b16;
}
.highlight .kd {
    color: #cb4b16;
}
.highlight .s,
.highlight .sb,
.highlight .sc,
.highlight .sd,
.highlight .s2,
.highlight .sh,
.highlight .sx,
.highlight .s1 {
    color: #859900;
}
.highlight .sr {
    color: #2aa198;
}
.highlight .si {
    color: #d33682;
}
.highlight .se {
    color: #d33682;
}
.highlight .nn {
    color: #b58900;
}
.highlight .nc {
    color: #b58900;
}
.highlight .no {
    color: #b58900;
}
.highlight .na {
    color: #268bd2;
}
.highlight .m,
.highlight .mf,
.highlight .mh,
.highlight .mi,
.highlight .il,
.highlight .mo,
.highlight .mb,
.highlight .mx {
    color: #859900;
}
.highlight .ss {
    color: #859900;
}
```

保存配置，重启 Zotero。

It works.

![](/media/files/2016-08-10-it-works.jpeg)


# Reference

1. [学习组块， 我喜欢称之为 chunks](https://www.quora.com/What-is-the-most-effective-way-to-learn)

2. [Zotero, a open source tool to collect, organize, cite, and share your research source](https://www.zotero.org/)

3. [Scrivener](https://www.literatureandlatte.com/scrivener.php)

4. [阳志平：Zotero（5）：电子文献管理攻略 ](http://www.yangzhiping.com/tech/zotero1.html)

5. [Zotero forum: How to change formats in the notes windows?](https://forums.zotero.org/discussion/48647/how-to-change-formats-in-the-notes-windows-/)

