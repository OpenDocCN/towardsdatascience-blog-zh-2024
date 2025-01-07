# 细看 Scipy 的 Stats 模块 — 第一部分

> 原文：[`towardsdatascience.com/a-closer-look-at-scipys-stats-module-part-1-5071858f32c1?source=collection_archive---------9-----------------------#2024-09-19`](https://towardsdatascience.com/a-closer-look-at-scipys-stats-module-part-1-5071858f32c1?source=collection_archive---------9-----------------------#2024-09-19)

## 让我们学习 Python 中 `scipy.stats` 模块的主要方法。

[](https://gustavorsantos.medium.com/?source=post_page---byline--5071858f32c1--------------------------------)![Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--5071858f32c1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5071858f32c1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5071858f32c1--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--5071858f32c1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5071858f32c1--------------------------------) ·阅读时间 7 分钟 ·2024 年 9 月 19 日

--

![](img/5ec650274c219deb0fc184845acdc08a.png)

图片由 [Алекс Арцибашев](https://unsplash.com/@lxrcbsv?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/a-bar-chart-is-shown-on-a-blue-background-vVHXeu0YNbk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 引言

在这个领域有足够经验的数据显示科学家和程序员，且选择 Python 作为编程语言的人，肯定已经听说过 `Scipy` 或科学 Python。

`Scipy` 是一个用于（如其所示）科学运算和计算的 Python 包。在这篇文章中，我们将更具体地讨论 `scipy.stats` 模块，这是用于 Python 中统计测试的模块。

# Scipy Stats

`Scipy.stats` 是科学 Python 中功能强大的统计模块。它是一个资源丰富的模块，提供了许多用于创建随机分布、进行统计测试、重抽样、变换、蒙特卡罗模拟等方法。

在这篇文章的第一部分，我们将探索分布、统计数据和假设检验。

接下来，在第二篇文章中，我们将探讨处理多个样本、重抽样和变换的工具。

让我们深入了解一下 Scipy 统计模块的主要方法。

## 分布
