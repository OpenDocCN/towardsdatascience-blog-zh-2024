# 中心极限定理的证明

> 原文：[`towardsdatascience.com/a-proof-of-the-central-limit-theorem-8be40324da83?source=collection_archive---------2-----------------------#2024-04-02`](https://towardsdatascience.com/a-proof-of-the-central-limit-theorem-8be40324da83?source=collection_archive---------2-----------------------#2024-04-02)

![](img/ca2fe597566482068a4bebeca042a52b.png)

图片由作者提供

## 如果你喜欢巧克力，这个证明将像是一块 Mars 巧克力棒的多层魔力。

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--8be40324da83--------------------------------)![Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--8be40324da83--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8be40324da83--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8be40324da83--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--8be40324da83--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8be40324da83--------------------------------) ·阅读时间 24 分钟 ·2024 年 4 月 2 日

--

本质上，证明中心极限定理有两种方式。第一种是经验方法，第二种则运用了数学思维那种静谧而完美的精准性。我将在本文中介绍这两种方法。

在经验方法中，你将亲眼看到中心极限定理的运作，或者可以说是部分运作，甚至在你实验所创造的雪花球宇宙中完全无法奏效。经验方法并不是在证明中心极限定理，而是在验证它在给定数据上的有效性。

我将进行这个实验，但不会使用合成模拟数据。我将使用实际的物理物体——那些你可以用手指捡起，拿在眼前，甚至放进嘴里的物体。我将测试这个实验的结果是否符合正态分布。

理论方法是对中心极限定理的完整数学证明，它涉及到五个概念的堆叠：

1.  泰勒定理（从中衍生出泰勒级数）

1.  矩生成函数

1.  泰勒级数

1.  生成函数

1.  无限序列和级数
