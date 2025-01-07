# 如何使用 NetworkX 提取图形特征以进行机器学习

> 原文：[https://towardsdatascience.com/how-to-extract-graph-based-features-for-machine-learning-with-networkx-302fc851955e?source=collection_archive---------2-----------------------#2024-09-03](https://towardsdatascience.com/how-to-extract-graph-based-features-for-machine-learning-with-networkx-302fc851955e?source=collection_archive---------2-----------------------#2024-09-03)

## 图形机器学习 — 从零到英雄

## 使用 Python 的逐步指南

[](https://medium.com/@giuseppefutia?source=post_page---byline--302fc851955e--------------------------------)[![Giuseppe Futia](../Images/4d1d3b3766eca9ae8220dc5eb480a4cf.png)](https://medium.com/@giuseppefutia?source=post_page---byline--302fc851955e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--302fc851955e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--302fc851955e--------------------------------) [Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--302fc851955e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--302fc851955e--------------------------------) ·阅读时间 17 分钟·2024年9月3日

--

图是非常重要的结构，能够建模各个领域中实体之间的连接关系，涵盖了如在线社交平台、交通网络和生物系统等多种场景。通过从这些图中提取有意义的特征，我们能够获得宝贵的洞察并提升机器学习算法的性能。

在之前的文章中，我们展示了如何使用 NumPy 和 NetworkX 来表示图。在本文中，我们将探索如何使用 NetworkX 在不同层次（节点、边和图本身）提取重要的图形特征。

[](/how-to-represent-graph-structures-from-numpy-to-networkx-047043bdf807?source=post_page-----302fc851955e--------------------------------) [## 如何表示图结构 — 从 NumPy 到 NetworkX

### 让我们理解如何使用 Python 创建和可视化网络信息

towardsdatascience.com](/how-to-represent-graph-structures-from-numpy-to-networkx-047043bdf807?source=post_page-----302fc851955e--------------------------------)

我们将使用 [Zachary 的空手道俱乐部网络](https://networkx.org/documentation/stable/auto_examples/graph/plot_karate_club.html)，这是 NetworkX 提供的一个示例图（[查看许可证详情](https://github.com/networkx/nx-guides/blob/main/LICENSE)）。这个广为人知的数据集代表了一个大学空手道俱乐部的社交网络，是理解基于图的特征提取的一个绝佳起点。

在深入细节之前，让我们定义一些帮助我们显示图表的代码。首先，我们介绍一些辅助函数来指定默认的可视化选项。在下面的代码中，你将看到两个函数…
