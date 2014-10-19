---
title: 间隔记忆工具：Anki
layout: post
guid: lu4uy7mt1mj3
date: 2014-10-19 20:25:00
tags:
   - Learning
---

![Apple]({{ site.url }}/media/files/2014/Oct/2014-10-19-anki-logo.png)

我的 Evernote 约有 3000 条笔记，有3个大分类：医学、产品、编程。为了对抗遗忘，需要经常分享这些知识给其他人，也要隔三差五的复习。我是 Evernote 的重度用户，但它已经远远满足不了我的需求了。

1. 笔记数量太多，每次蜻蜓点水似的复习也需要 1.5 个小时。

2. 有些知识点已经转化为长时记忆，几周复习一次就可以。有的知识还是短时记忆，只活在海马区，需要在短时间内高频的复习。

3. 假如单条笔记中涵盖4个知识点，3个记住，1个遗忘。为了找出那1个漏网之鱼，我要通读整条笔记。

    Git 的存储对象有哪几种类型？各自的用途是什么？
    * Commit (已经记牢)
    * Tree (已经记牢)
    * Blog (已经记牢)
    * Tag (忘记了...)


一直以来，我在期盼一个更好的复习工具：

1. 更节省时间。

2. 能够按照记忆曲线帮我安排复习任务。

3. 帮我标记已遗忘的知识点，复习时更有针对性。

直到有一天，遇到了 Anki。

## 1. Flashcard

![Apple]({{ site.url }}/media/files/2014/Oct/2014-10-19-apple-flashcard.jpg)

Flashcard 就是抽卡，正面是一个问题（或图片），反面是答案。每个 flashcard 就是一个简单的自我测试。

Anki 就是一个 flashcard 管理工具。你把各种知识点整理成一个个 Question-Answer 的形式，保存在 Anki 中。

## 2. 如何使用 Anki 复习？

![Review]({{ site.url }}/media/files/2014/Oct/2014-10-19-review.png)

Anki 的复习过程，就是拿一堆 flashcard 测试自己，自己回答后翻开背面，比对答案。

1. 答对了，证明你记住了。Anki 会让该知识点在一个相当长的时间后再出现。

2. 答错了，证明你没记住。Anki 会把该知识点的复习频率调的更高。


## 3. 间隔记忆（Spaced Repetition System）

![Leitner]({{ site.url }}/media/files/2014/Oct/2014-10-19-leitner.gif)

Anki 的设计是基于间隔记忆理论。

In this method flashcards are sorted into groups according to how well you know each one in the Leitner's learning box. This is how it works: you try to recall the solution written on a flashcard. If you succeed, you send the card to the next group. But if you fail, you send it back to the first group. Each succeeding group has a longer period of time before you are required to revisit the cards.


## 4. Anki 为什么高效？

1. Anki 采用了 test 的形式，每一个 flashcard 都是对自己的测试，需要强迫自己开动大脑 recall。比单纯的「看笔记」效率高。

2. 新知识复习频率高，旧知识复习频率低，遵循记忆曲线。

3. 遗忘的知识多复习，熟记的知识少复习。

## 5. 总结

Anki 的配置文件相当复杂，复习频率，延长周期，每天复习的数量等等都是可以设置。零零散散花了一个月设置好好适合我的 config 文件，但这时间花的绝对值。

它就像一个记忆管理系统，井井有条的管理你的记忆。在你似忘非忘时，恰到好处的问一句：「Git 的存储对象有哪四种类型？」


## 6. 参考资料

1. [Anki User Mannual](http://ankisrs.net/docs/manual.html)

2. [Download Anki Android for free](https://play.google.com/store/apps/details?id=com.ichi2.anki)

3. [Anki for iPhone/iPad/iPod Touch](https://itunes.apple.com/us/app/ankimobile-flashcards/id373493387?mt=8&uo=4&at=11lJ6Y)