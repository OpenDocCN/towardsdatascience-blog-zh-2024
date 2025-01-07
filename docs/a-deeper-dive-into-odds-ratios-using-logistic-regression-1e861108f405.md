# 深入探讨使用逻辑回归的优势比

> 原文：[`towardsdatascience.com/a-deeper-dive-into-odds-ratios-using-logistic-regression-1e861108f405?source=collection_archive---------6-----------------------#2024-10-08`](https://towardsdatascience.com/a-deeper-dive-into-odds-ratios-using-logistic-regression-1e861108f405?source=collection_archive---------6-----------------------#2024-10-08)

## 《深入探讨优势比系列》第二部分

## 本文是关于如何使用 Python 和 Statsmodels 从逻辑回归模型中提取和探索优势比（odds ratios）的全面指南，并附带示例。

[](https://miqbalrp.medium.com/?source=post_page---byline--1e861108f405--------------------------------)![Iqbal Rahmadhan](https://miqbalrp.medium.com/?source=post_page---byline--1e861108f405--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1e861108f405--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e861108f405--------------------------------) [Iqbal Rahmadhan](https://miqbalrp.medium.com/?source=post_page---byline--1e861108f405--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e861108f405--------------------------------) ·阅读时间：18 分钟·2024 年 10 月 8 日

--

![](img/29c607e662f61c677ce3c8e83a7b6394.png)

图片由 [NEOM](https://unsplash.com/@neom?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当我们构建一个统计模型时，我们通常更关注它的预测价值。然而，我们也可以利用它揭示数据背后的故事。

> **还不是 Medium 会员？继续使用这个** **免费版本****！**

逻辑回归是最简单也是最有效的二分类模型之一。除了预测外，我们还可以从拟合的逻辑回归模型中获取每个变量的优势比，这对于我们理解数据至关重要。

在本文中，作为《深入探讨优势比系列》第一篇文章的延续，我们将探讨如何从逻辑回归中提取优势比。我们将首先推导模型与优势比之间的关系。然后，我们将研究一些使用逻辑回归方法的案例，这些案例展示了它在计算优势比方面比基本方法的优势，特别是在：对分类变量和数值变量的计算、处理多个变量时的优势等。
