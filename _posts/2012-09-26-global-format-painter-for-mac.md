---
layout: post
title: Mac 全局格式刷工具
category: work
---

用习惯了微软Office系列的格式刷，那整理起文章是得心应手啊，掌握了绝对不会有字体高矮各异，间距时宽时窄的问题。可是，Office系列软件是舒心了，不支持格式刷的App应用、浏览器这些都该怎么办呢？

为了解决这些问题，全局格式刷工具正式入驻Mac了。简单四步操作，解决格式烦恼：

1.  选中源格式文本
	![选中源格式文本](http://res.toraleap.com/images/20120926/FormatPainter01.png)
2.  按下⌘C复制源文本，此时在状态栏可直观看到当前复制文字的格式概要
	![复制源文本](http://res.toraleap.com/images/20120926/FormatPainter02.png)
3.  选中目标文本
	![选中目标文本](http://res.toraleap.com/images/20120926/FormatPainter03.png)
4.  按下⇧⌘V粘贴格式到目标文本
	![粘贴格式](http://res.toraleap.com/images/20120926/FormatPainter04.png)
点击下载：[FormatPainter.dmg](http://res.toraleap.com/attachments/FormatPainter.dmg) (79KB)

如对工作原理有兴趣，请继续看：

1.  监视剪贴板变化，并在状态栏显示从剪贴板中分析出的文本格式；
2.  当按下程序热键⇧⌘V时，做如下工作：
    1.  分析当前剪贴板中的文本格式并暂存；
    2.  向当前应用程序发送⌘C按键组合，使应用把选中的目标文本存入剪贴板；
    3.  从剪贴板中获取目标文本，去除现有格式信息；
    4.  对目标文本和先前暂存的格式信息进行合成，得到应用了新格式的文本；
    5.  将应用了新格式的目标文本存入剪贴板；
    6.  向当前应用程序发送⌘V按键组合，用新格式文本替换选中的目标文本。
P.S. 这是我写的第二个Mac App，练手之作，若有Bug，请多包涵～