# 介绍 Path-Swarm 和 Super-Swarm：下一代蜂群图

> 原文：[https://towardsdatascience.com/introducing-path-swarm-super-swarm-next-level-swarm-charts-03605ea87d58?source=collection_archive---------4-----------------------#2024-07-18](https://towardsdatascience.com/introducing-path-swarm-super-swarm-next-level-swarm-charts-03605ea87d58?source=collection_archive---------4-----------------------#2024-07-18)

## 了解 Path-Swarm 图表，并使用 Python 创建你自己的图表

[](https://medium.com/@nickgerend?source=post_page---byline--03605ea87d58--------------------------------)[![Nick Gerend](../Images/716eb183008674ac46c6aee96093c4b3.png)](https://medium.com/@nickgerend?source=post_page---byline--03605ea87d58--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--03605ea87d58--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--03605ea87d58--------------------------------) [Nick Gerend](https://medium.com/@nickgerend?source=post_page---byline--03605ea87d58--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03605ea87d58--------------------------------) ·37分钟阅读·2024年7月18日

--

![](../Images/9a14e796643e10cc8991f7b31fef75e5.png)

Nick Gerend 的 Path Swarm 和 Super Swarm 图表

# 引言

本文将重点介绍一种数据可视化技术，它使用圆形排列来表示单个观察值。在我上一篇文章中，我回顾了一种将方形排列应用于数据的方法（我称之为 [Quad-Tile 图表](https://medium.com/towards-data-science/introducing-the-quad-tile-chart-squaremap-squarify-your-data-20be336a1dd7)），作为一种替代更常见的基于圆形的布局方式，但现在我将回归使用圆形布局，进一步扩展蜂群图。

我们将探讨将基础的蜂群图组件应用于创建新型视觉布局的全新方式，从而将蜂群图提升到另一个灵活性水平！

以下内容将展示两种新的主要蜂群技术，并附带一些有趣的扩展，能够轻松地应用于数据中，用于快速可视化探索数值和分类特征。有关类似数据的更多探索性代码练习，请参阅上一篇文章的引言部分：
