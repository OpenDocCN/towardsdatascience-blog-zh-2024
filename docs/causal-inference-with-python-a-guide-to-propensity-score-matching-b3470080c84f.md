# 使用 Python 进行因果推断：倾向得分匹配指南

> 原文：[`towardsdatascience.com/causal-inference-with-python-a-guide-to-propensity-score-matching-b3470080c84f?source=collection_archive---------0-----------------------#2024-07-02`](https://towardsdatascience.com/causal-inference-with-python-a-guide-to-propensity-score-matching-b3470080c84f?source=collection_archive---------0-----------------------#2024-07-02)

## 介绍如何在非随机化环境下使用实际示例和 Python 代码估计治疗效应

[](https://medium.com/@lukasz.szubelak?source=post_page---byline--b3470080c84f--------------------------------)![Lukasz Szubelak](https://medium.com/@lukasz.szubelak?source=post_page---byline--b3470080c84f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b3470080c84f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b3470080c84f--------------------------------) [Lukasz Szubelak](https://medium.com/@lukasz.szubelak?source=post_page---byline--b3470080c84f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b3470080c84f--------------------------------) ·23 分钟阅读·2024 年 7 月 2 日

--

![](img/d19675cc1687b1925f3d751ce7c4f51b.png)

图片由[Isaac Smith](https://unsplash.com/@isaacmsmith?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

评估治疗或干预的影响在多个领域至关重要，无论是在商业还是公共领域。确定某个特定行动是否产生了预期的效果对于做出明智的决策至关重要。虽然随机实验被认为是这种评估的金标准，但它们并非总是可行的。

在这些情况下，可以使用各种因果推断方法来估计治疗效应。本文描述了在因果推断研讨会上使用的强大方法：**倾向得分匹配**，并提供了该分析技术的指南。

**什么是倾向得分匹配？**

倾向得分匹配（PSM）允许我们根据治疗组和未治疗组个体的相似性构建一个人工对照组。在应用 PSM 时，我们将每个治疗单位与具有相似特征的未治疗单位匹配。

通过这种方式，我们可以在没有随机实验的情况下获得一个对照组。这个人工对照组将由那些与治疗组相似的未治疗单位组成…
