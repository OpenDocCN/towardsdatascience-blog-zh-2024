# R 中的二项逻辑回归

> 原文：[https://towardsdatascience.com/binary-logistic-regression-in-r-dff4e1dc093b?source=collection_archive---------12-----------------------#2024-01-30](https://towardsdatascience.com/binary-logistic-regression-in-r-dff4e1dc093b?source=collection_archive---------12-----------------------#2024-01-30)

## 学习何时以及如何在 R 中使用（单变量和多变量）二项逻辑回归。还要学习如何解释、可视化和报告结果。

[](https://antoinesoetewey.medium.com/?source=post_page---byline--dff4e1dc093b--------------------------------)[![Antoine Soetewey](../Images/51d7837d18ff15a62cac2343a485e35d.png)](https://antoinesoetewey.medium.com/?source=post_page---byline--dff4e1dc093b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dff4e1dc093b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dff4e1dc093b--------------------------------) [Antoine Soetewey](https://antoinesoetewey.medium.com/?source=post_page---byline--dff4e1dc093b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dff4e1dc093b--------------------------------) ·46分钟阅读·2024年1月30日

--

![](../Images/b1d5aed06f84dbc785fc29a71a31f32a.png)

图片来源：[Annie Spratt](https://unsplash.com/@anniespratt?utm_source=medium&utm_medium=referral)

# 介绍

回归分析是统计学中常用的工具，用于检验和量化变量之间的关系。

最常见的两种回归是线性回归和逻辑回归。当因变量是**定量**时，使用**线性**回归；而当因变量是**定性**时，则使用**逻辑**回归。

线性回归和逻辑回归有不同的类型。在详细介绍之前，我们先回顾一下变量的类型。

定量变量衡量的是数量，它可以取的值是数字。它被分为：

+   离散型：它可以取的值是可计数的，且具有有限的可能性（这些值通常是整数，例如子女数量），以及

+   连续型：它可以取的值是不可计数的，并且有无限多的可能性（这些值通常带有小数，或者至少在技术上可能带小数，例如体重）。
