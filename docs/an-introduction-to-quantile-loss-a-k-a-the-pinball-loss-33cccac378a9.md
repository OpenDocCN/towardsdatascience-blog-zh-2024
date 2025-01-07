# 分位损失简介，亦称为弹球损失

> 原文：[`towardsdatascience.com/an-introduction-to-quantile-loss-a-k-a-the-pinball-loss-33cccac378a9?source=collection_archive---------1-----------------------#2024-08-04`](https://towardsdatascience.com/an-introduction-to-quantile-loss-a-k-a-the-pinball-loss-33cccac378a9?source=collection_archive---------1-----------------------#2024-08-04)

![](img/774ca01180d5c0b5d2ef302ea5ec1859.png)

使用 Midjourney 生成的图像

## 了解用于评估概率预测的指标背后的直觉

[](https://eryk-lewinson.medium.com/?source=post_page---byline--33cccac378a9--------------------------------)![Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--33cccac378a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--33cccac378a9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33cccac378a9--------------------------------) [Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--33cccac378a9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33cccac378a9--------------------------------) ·7 分钟阅读·2024 年 8 月 4 日

--

有很多关于[回归评估指标](https://medium.com/towards-data-science/a-comprehensive-overview-of-regression-evaluation-metrics-6264af0926db)的文章，如 MSE、MAE、RMSE 等。这些指标在我们关注均值或中位数预测时非常重要。然而，当我们希望训练模型以关注分布中的其他位置时，我们必须使用一种不同的指标，这种指标在数据科学博客文章中并不常见。

在本文中，我们将探讨分位损失，也称为弹球损失，这是分位回归中的首选指标。

# 几个定义，帮助我们入门

在解释分位损失之前，让我们快速浏览几个定义，以确保我们在同一页上。

让我们从一个简单的例子开始。属于**回归**类型的算法预测一个连续变量，例如，它们预测温度、股票价格、最新 iPhone 的需求等。

现在是时候进行统计学回顾了。**α分位数**是一个值，它将给定的一组数字分开，使得α × 100%的数字小于或等于此值，而剩余的(1 − α) × 100%的数字大于该值…
