# 决策树和梯度提升的可视化理解

> 原文：[`towardsdatascience.com/a-visual-understanding-of-decision-trees-and-gradient-boosting-c6bc53f982ce?source=collection_archive---------0-----------------------#2024-07-26`](https://towardsdatascience.com/a-visual-understanding-of-decision-trees-and-gradient-boosting-c6bc53f982ce?source=collection_archive---------0-----------------------#2024-07-26)

## 决策树和梯度提升背后的数学原理的可视化解释

[](https://reza-bagheri79.medium.com/?source=post_page---byline--c6bc53f982ce--------------------------------)![Reza Bagheri](https://reza-bagheri79.medium.com/?source=post_page---byline--c6bc53f982ce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c6bc53f982ce--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c6bc53f982ce--------------------------------) [Reza Bagheri](https://reza-bagheri79.medium.com/?source=post_page---byline--c6bc53f982ce--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c6bc53f982ce--------------------------------) ·39 分钟阅读·2024 年 7 月 26 日

--

![](img/1316e40940c1a29a9c9b6262d983bb1b.png)

图片由 DALL.E 生成

决策树是一种非参数化的监督学习算法，可用于分类和回归。它使用树状结构来表示决策及其潜在结果。决策树简单易懂，便于解释，并且可以轻松可视化。然而，当决策树模型变得过于复杂时，它不能很好地从训练数据中进行泛化，导致过拟合。

梯度提升是一个集成学习模型，我们将多个弱学习器组合起来，开发一个强学习器。这些弱学习器是单独的决策树，每个学习器都尝试聚焦于前一个学习器的错误。与单一的深度决策树相比，梯度提升通常不容易发生过拟合。

本文将通过可视化的方式解释决策树在分类和回归问题中的直觉。我们将看到这个模型是如何工作的，以及为什么它可能导致过拟合。接下来，我们将介绍梯度提升，并了解它如何提高单个决策树的性能。将从头开始在 Python 中实现一个梯度提升回归器和分类器。最后，数学原理...
