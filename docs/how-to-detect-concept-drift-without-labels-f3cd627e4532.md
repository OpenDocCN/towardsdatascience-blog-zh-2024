# 如何在没有标签的情况下检测概念漂移

> 原文：[`towardsdatascience.com/how-to-detect-concept-drift-without-labels-f3cd627e4532?source=collection_archive---------6-----------------------#2024-03-12`](https://towardsdatascience.com/how-to-detect-concept-drift-without-labels-f3cd627e4532?source=collection_archive---------6-----------------------#2024-03-12)

## 使用参考窗口进行无监督变化检测，并附有 Python 示例

[](https://vcerq.medium.com/?source=post_page---byline--f3cd627e4532--------------------------------)![Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--f3cd627e4532--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f3cd627e4532--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f3cd627e4532--------------------------------) [Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--f3cd627e4532--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f3cd627e4532--------------------------------) ·6 分钟阅读·2024 年 3 月 12 日

--

![](img/378e03f383f18e6b0a55f0f9733ec2c4.png)

图片由[Chris Czermak](https://unsplash.com/@chris_czermak?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在[上一篇文章](https://medium.com/towards-data-science/understanding-concept-drift-a-simple-guide-b2cf4e09deae)中，我们探讨了概念漂移的基础知识。当数据集的分布发生变化时，就会发生概念漂移。

本文将继续探讨这一主题。在这里，你将学习如何在没有标签的情况下检测概念漂移。这个任务具有挑战性，因为没有标签我们无法评估模型的表现。

让我们深入了解。

# 介绍

随着时间变化的数据集容易受到概念漂移的影响。分布的变化可能会破坏模型及其预测的准确性。因此，检测并适应这些变化至关重要，以保持模型的最新性。

大多数变化检测方法依赖于追踪模型的错误。其思路是，当错误显著增加时触发警报。然后，某种适应机制启动，例如重新训练模型。

在[上一篇文章](https://medium.com/towards-data-science/understanding-concept-drift-a-simple-guide-b2cf4e09deae)中，我们讨论了在某些情况下获取标签可能会很困难的问题。这种情况在许多领域中都有出现，例如欺诈检测或信用风险评估。在后者中，个人违约所需的时间（以及提供标签的时间）是一个关键因素……
