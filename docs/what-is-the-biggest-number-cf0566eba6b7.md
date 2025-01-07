# 最大的数字是什么？

> 原文：[https://towardsdatascience.com/what-is-the-biggest-number-cf0566eba6b7?source=collection_archive---------6-----------------------#2024-01-31](https://towardsdatascience.com/what-is-the-biggest-number-cf0566eba6b7?source=collection_archive---------6-----------------------#2024-01-31)

## 在我们称之为家的抽象世界中，我们需要多少位数？一段非技术性的旅程，追寻所有数字的最长和

[](https://medium.com/@JamesW1?source=post_page---byline--cf0566eba6b7--------------------------------)[![Jimmy W](../Images/2eaffb6f171b7d96886bc0b5c516aa17.png)](https://medium.com/@JamesW1?source=post_page---byline--cf0566eba6b7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cf0566eba6b7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cf0566eba6b7--------------------------------) [Jimmy W](https://medium.com/@JamesW1?source=post_page---byline--cf0566eba6b7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf0566eba6b7--------------------------------) ·9分钟阅读·2024年1月31日

--

![](../Images/390253a43c7e2b8fa5b4a40239bb8145.png)

图片由[Giordano Rossoni](https://unsplash.com/@giordanorossoni?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，[来源](https://unsplash.com/photos/a-spiral-staircase-in-a-building-with-graffiti-on-it-czu8X_gfpP0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

TLDR：**最大的数字有16位**

问问一位天文学家，我们便进入了亿万年的宇宙旅行，超越了宇宙。

问问一个微观物理学家，我们就进入了神秘的夸克和前子，万物的基本构件。

问问我的5岁孩子，你会得到643。

这是一个每个人都能猜的谜题，但当你深入探讨时，它出奇地个人化。

## 无限大

我把它当作虚数来处理，像无限大一样，视其为一种炼金术，一种符号，而非一个可实际操作的数字。它永远不会出现在卷尺上。

这里是参数：

**1\. 每个数字都必须有意义：**

宇宙从一端到另一端的估计距离是930亿光年，这听起来很震撼，但它可能是930.4亿或930.5亿光年，两个答案都可以接受。要获取最大数字所要求的精细程度，就需要应用才能使它变得有意义。从宇宙一端到另一端的旅行很伟大，但……

> “你真的不喜欢这个答案”
