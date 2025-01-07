# 如何计算 PPC 营销中竞价的弹性

> 原文：[`towardsdatascience.com/how-to-calculate-the-elasticity-of-bids-in-ppc-marketing-1a989c0b46cd?source=collection_archive---------10-----------------------#2024-10-03`](https://towardsdatascience.com/how-to-calculate-the-elasticity-of-bids-in-ppc-marketing-1a989c0b46cd?source=collection_archive---------10-----------------------#2024-10-03)

## 价格弹性是计量经济学的核心内容。那么，我们如何将弹性应用于竞价呢？

[](https://medium.com/@joparga3?source=post_page---byline--1a989c0b46cd--------------------------------)![Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--1a989c0b46cd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1a989c0b46cd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1a989c0b46cd--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--1a989c0b46cd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1a989c0b46cd--------------------------------) ·9 分钟阅读·2024 年 10 月 3 日

--

![](img/506d9e318cb68a5566e6a5c8bb32b7f3.png)

图像由 Dall-E 创作

价格弹性是计量经济学的核心内容。通常你听到的是“[需求弹性](https://www.google.com/search?client=safari&rls=en&q=elasticity+of+demand&ie=UTF-8&oe=UTF-8)”，其中你试图理解如果我们提高价格一定幅度，需求会增加多少。在竞价生态系统中，市场营销人员尝试理解如果我们将竞价提高 X%，会获得多少“点击”。在本文中，我将向你展示如何在 PPC 营销中计算竞价的弹性。

*PS 1: 本文中的例子来源于我管理的 100 多个活动中的 1 个* [*Skyscanner*](https://www.skyscanner.net/)*。为了保护共享信息，我已移除所有标识符，但你将能够看到如何利用真实分布数据计算出竞价的弹性。*

*PS 2: 所有图片除非另有说明，均由我本人创作。*

# 供给弹性

在深入计算竞价弹性之前，让我们回到计量经济学 101，了解什么是供给弹性。直接引用[这篇文章](https://www.investopedia.com/ask/answers/040615/how-does-price-elasticity-affect-supply.asp)：“价格弹性供给表示生产者在价格变化时调整生产水平的速度。” [1]如…
