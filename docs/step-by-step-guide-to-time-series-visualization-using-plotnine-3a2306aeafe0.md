# 使用Plotnine进行时间序列可视化的逐步指南

> 原文：[https://towardsdatascience.com/step-by-step-guide-to-time-series-visualization-using-plotnine-3a2306aeafe0?source=collection_archive---------6-----------------------#2024-03-27](https://towardsdatascience.com/step-by-step-guide-to-time-series-visualization-using-plotnine-3a2306aeafe0?source=collection_archive---------6-----------------------#2024-03-27)

## 探索时间序列的6种图形

[](https://vcerq.medium.com/?source=post_page---byline--3a2306aeafe0--------------------------------)[![Vitor Cerqueira](../Images/9e52f462c6bc20453d3ea273eb52114b.png)](https://vcerq.medium.com/?source=post_page---byline--3a2306aeafe0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3a2306aeafe0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3a2306aeafe0--------------------------------) [Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--3a2306aeafe0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a2306aeafe0--------------------------------) ·7分钟阅读·2024年3月27日

--

![](../Images/aa7da83e1867f62b7212e819dc08092f.png)

由[Alex Litvin](https://unsplash.com/@alexlitvin?utm_source=medium&utm_medium=referral)拍摄，[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

可视化是一种快速且有效的方式，从数据中获取洞察。本文提供了一个逐步指南，用于使用图形探索时间序列。

我们将使用6种不同的图表来揭示时间序列的不同方面。我们将重点介绍Python的plotnine，这是一个图形语法类型的库。

# 介绍

探索性数据分析是一种旨在揭示数据集底层结构的方法。几乎总是，这个过程涉及使用图形技术来可视化数据。

使用图形进行时间序列分析是一种快速从数据中提取洞察的方法，例如：

+   发现基本模式，如趋势或季节性

+   检测异常，包括缺失数据或离群值

+   检测分布的变化

在本文的其余部分，您将学习如何构建6种图形来探索时间序列。

# 探索时间序列

让我们从加载一个时间序列开始。在本指南中，我们将使用M3数据集[2]中的一个月度时间序列。我们…
