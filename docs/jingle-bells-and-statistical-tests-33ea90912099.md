# 《铃儿响叮当与统计检验》

> 原文：[https://towardsdatascience.com/jingle-bells-and-statistical-tests-33ea90912099?source=collection_archive---------5-----------------------#2024-12-25](https://towardsdatascience.com/jingle-bells-and-statistical-tests-33ea90912099?source=collection_archive---------5-----------------------#2024-12-25)

## 数据类型、假设及适用于它们的统计检验，通过节日圣诞市场示例来解释🎄🎅🎡

[](https://gizkaya.medium.com/?source=post_page---byline--33ea90912099--------------------------------)[![Gizem Kaya](../Images/29d29f04df742f8c19cb9fb0c7169e5d.png)](https://gizkaya.medium.com/?source=post_page---byline--33ea90912099--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--33ea90912099--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--33ea90912099--------------------------------) [Gizem Kaya](https://gizkaya.medium.com/?source=post_page---byline--33ea90912099--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33ea90912099--------------------------------) ·6分钟阅读·2024年12月25日

--

![](../Images/bd3ae6953bd306679e59b3dde2c9c709.png)

照片由[Humphrey Muleba](https://unsplash.com/@good_citizen?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这是一年中那段神奇的时光。闪烁的灯光和闪亮的装饰品让人眼前一亮；而礼物、笑声、家庭时光和热腾腾的红酒温暖着心灵。尽管寒冷的冬天依旧肆虐，身处人群中，和大家一起享受这些美妙的时刻，依然带来一份温馨的喜悦。

这真的是一种工作危害——连续三天在圣诞市场游荡，我不禁开始从统计分析的角度看待一切。然后我恍然大悟，为什么不通过圣诞节的可爱例子来解释统计检验，让它们更加有趣且容易理解呢？祝所有庆祝的人们圣诞快乐，充满爱、笑声，当然还有热红酒（glühwein）。祝阅读愉快！

让我们先来回顾一下**统计检验**到底是什么。它们是用于对数据进行推断的基本工具。它有点像是试图预测圣诞市场的人群规模——我们提出一个假设，并进行检验，看看我们是否正确（或者完全错了！）。我们提出一个关于研究问题的陈述——一个假设，并使用适当的技术——统计检验——来接受或拒绝它…
