# 开始问你的数据“为什么？”——因果关系的温和入门

> 原文：[`towardsdatascience.com/%EF%B8%8F-start-asking-your-data-why-a-gentle-intro-to-causality-47142d5ec96a?source=collection_archive---------2-----------------------#2024-09-12`](https://towardsdatascience.com/%EF%B8%8F-start-asking-your-data-why-a-gentle-intro-to-causality-47142d5ec96a?source=collection_archive---------2-----------------------#2024-09-12)

## 一份关于超越相关性思维的初学者指南。

[](https://eyal-kazin.medium.com/?source=post_page---byline--47142d5ec96a--------------------------------)![Eyal Kazin](https://eyal-kazin.medium.com/?source=post_page---byline--47142d5ec96a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47142d5ec96a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47142d5ec96a--------------------------------) [Eyal Kazin](https://eyal-kazin.medium.com/?source=post_page---byline--47142d5ec96a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47142d5ec96a--------------------------------) ·阅读时间：16 分钟·2024 年 9 月 12 日

--

> 因果关系不仅仅是统计学的一个方面——它是对统计学的补充——Judea Pearl

![](img/9d0fa0d5064478ceb43e8d2095fa85dc.png)

牛顿摆。图片来源：[Pickpik](https://www.pickpik.com/newton-s-cradle-balls-sphere-action-reaction-illustration-806)。

相关性并不意味着因果关系。然而，事实证明，通过一些简单巧妙的技巧，我们可以在标准的观察数据中揭示因果关系，而无需依赖昂贵的随机对照试验。

本文面向任何做数据驱动决策的人。主要的核心信息是，因果关系是可能的，通过理解**数据背后的故事**和数据本身一样重要。

通过介绍*辛普森悖论*和*伯克森悖论*——即当一个总体的结果与其子群体的结果相冲突时——我强调了使用因果推理识别这些悖论并避免误解的重要性。具体来说，我介绍了*因果图*作为一种可视化数据背后故事的方法，并指出，通过将这一方法加入你的工具箱，你将能够进行更好的分析和实验。

我的最终目标是激发你对因果关系的兴趣，因为我相信通过…
