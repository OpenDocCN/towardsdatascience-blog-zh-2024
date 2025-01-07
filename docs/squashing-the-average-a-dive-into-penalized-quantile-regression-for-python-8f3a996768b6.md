# 压缩平均值：深入探讨 Python 中的惩罚分位回归

> 原文：[https://towardsdatascience.com/squashing-the-average-a-dive-into-penalized-quantile-regression-for-python-8f3a996768b6?source=collection_archive---------11-----------------------#2024-08-16](https://towardsdatascience.com/squashing-the-average-a-dive-into-penalized-quantile-regression-for-python-8f3a996768b6?source=collection_archive---------11-----------------------#2024-08-16)

## 如何构建惩罚分位回归模型（附代码！）

[](https://medium.com/@alvaromc317?source=post_page---byline--8f3a996768b6--------------------------------)[![Álvaro Méndez Civieta](../Images/7b181a6e739dfb0425a846de77d013bc.png)](https://medium.com/@alvaromc317?source=post_page---byline--8f3a996768b6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8f3a996768b6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8f3a996768b6--------------------------------) [Álvaro Méndez Civieta](https://medium.com/@alvaromc317?source=post_page---byline--8f3a996768b6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8f3a996768b6--------------------------------) ·5分钟阅读·2024年8月16日

--

![](../Images/4c73bf89e54dafc5f08dded128f0f692.png)

图片由 [Joes Valentine](https://unsplash.com/@joesvalentine?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 提供：想象这些是正态分布。

这是我关于惩罚回归系列的第三篇文章。在第一篇中，我们讨论了如何在 Python 中实现 [稀疏组套索](/sparse-group-lasso-in-python-255e379ab892)，这是目前回归模型中最好的变量选择方法之一；第二篇我们讨论了 [自适应估计量](https://medium.com/towards-data-science/an-adaptive-lasso-63afca54b80d)，以及它们如何优于传统的估计方法。今天，我想讨论一下**分位回归**，并深入探讨如何使用稳健的 `**asgl**` **包**进行高维分位回归，重点介绍带有自适应套索惩罚的分位回归实现。

## 今天我们将会看到：

+   什么是分位回归？

+   与传统的最小二乘回归相比，分位回归有哪些优势？

+   如何在 Python 中实现惩罚分位回归模型

## 什么是分位回归？

让我们从许多人可能已经遇到过的东西开始：**最小二乘回归**。当我们希望基于一些输入变量预测结果时，这通常是经典的首选方法。它……
