# 堆叠集成方法

> 原文：[`towardsdatascience.com/the-stacking-ensemble-method-984f5134463a?source=collection_archive---------3-----------------------#2024-02-29`](https://towardsdatascience.com/the-stacking-ensemble-method-984f5134463a?source=collection_archive---------3-----------------------#2024-02-29)

## 使用 scikit-learn 理解堆叠

[](https://mocquin.medium.com/?source=post_page---byline--984f5134463a--------------------------------)![Yoann Mocquin](https://mocquin.medium.com/?source=post_page---byline--984f5134463a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--984f5134463a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--984f5134463a--------------------------------) [Yoann Mocquin](https://mocquin.medium.com/?source=post_page---byline--984f5134463a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--984f5134463a--------------------------------) ·阅读时间 13 分钟·2024 年 2 月 29 日

--

探索机器学习中堆叠的力量——一种将多个模型组合成一个强大的预测器的技术。本文从基础到高级技术，探讨了堆叠，揭示了它如何融合多种模型的优点以提高准确性。无论你是堆叠的新手，还是在寻找优化策略，这篇指南都提供了实践性的见解和技巧，帮助你通过 scikit-learn 提升预测建模能力。

*尽管本文基于 scikit-learn，但我在文末提供了一个纯 Python 类，它实现并模仿了 scikit-learn 的堆叠模型。审视这个纯 Python 实现是一个极好的方式，可以帮助你检验和测试自己的理解。*

在这篇文章中，我们将看到：

+   堆叠是机器学习中集成技术的一部分

+   堆叠如何在内部工作以提供预测

+   如何进行模型拟合

+   什么是“重堆叠”

+   如何创建多层堆叠模型

+   我们为什么以及如何检查基础模型的性能

+   如何调优和优化堆叠模型的使用

![](img/e38a73a60e65b3cf71db9018b0b3c24e.png)

图片来自 [Brigitte Tohm](https://unsplash.com/@brigittetohm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

> 如果你喜欢或想学习使用 scikit-learn 进行机器学习，请查看…
