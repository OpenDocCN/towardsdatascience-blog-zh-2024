# 超越预测：提升模型与影响力科学（第一部分）

> 原文：[`towardsdatascience.com/beyond-predictions-uplift-modeling-the-science-of-influence-part-i-cb28c47d1b04?source=collection_archive---------1-----------------------#2024-01-03`](https://towardsdatascience.com/beyond-predictions-uplift-modeling-the-science-of-influence-part-i-cb28c47d1b04?source=collection_archive---------1-----------------------#2024-01-03)

![](img/591483adbad52cbeeaa74d528e4f5af1.png)

作者插图

## 基于树模型的提升方法

[](https://linafaik.medium.com/?source=post_page---byline--cb28c47d1b04--------------------------------)![Lina Faik](https://linafaik.medium.com/?source=post_page---byline--cb28c47d1b04--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cb28c47d1b04--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb28c47d1b04--------------------------------) [Lina Faik](https://linafaik.medium.com/?source=post_page---byline--cb28c47d1b04--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb28c47d1b04--------------------------------) ·阅读时间：14 分钟·2024 年 1 月 3 日

--

预测分析一直是决策制定的基石，但如果我们告诉你，除了预测之外，还有另一种选择呢？如果你能够战略性地影响结果呢？

提升模型承载着这一承诺。它通过识别那些在接受特殊处理后行为可以积极影响的个体，为传统的预测方法增加了一个有趣的动态层面。

应用案例是无穷无尽的。在医学领域，它可以帮助识别那些通过医疗治疗能够改善健康的患者。在零售行业，这样的模型可以更好地定位那些能够通过促销或个性化服务提高客户保持率的客户。

# 目标

本文是一个系列文章的第一部分，探讨了提升模型的变革潜力，阐明了它如何重塑市场营销、医疗等领域的策略。本文重点介绍基于决策树的提升模型，并以促销优惠的应用为案例，预测客户转化率。
