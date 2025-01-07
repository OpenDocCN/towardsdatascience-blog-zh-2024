# 如何表示图结构 — 从 NumPy 到 NetworkX

> 原文：[`towardsdatascience.com/how-to-represent-graph-structures-from-numpy-to-networkx-047043bdf807?source=collection_archive---------5-----------------------#2024-08-14`](https://towardsdatascience.com/how-to-represent-graph-structures-from-numpy-to-networkx-047043bdf807?source=collection_archive---------5-----------------------#2024-08-14)

## 图形机器学习 — 从零到英雄

## 让我们了解如何使用 Python 创建和可视化网络信息

[](https://medium.com/@giuseppefutia?source=post_page---byline--047043bdf807--------------------------------)![Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--047043bdf807--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--047043bdf807--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--047043bdf807--------------------------------) [Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--047043bdf807--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--047043bdf807--------------------------------) ·10 分钟阅读·2024 年 8 月 14 日

--

图是基本的数据结构，用于表示不同领域中实体之间的关系，包括社交网络、网页、交通网络和学术联系。这些领域中的关系各不相同，因此我们需要采用不同类型的图来尽可能精确地匹配这些连接的性质。

本文探讨了如何使用 Python 构建和表示多种图形，利用 NumPy 和 NetworkX 库。更具体地说，我们使用 NumPy 通过邻接矩阵来描述连接结构，并使用 NetworkX 来可视化这些结构并理解它们之间的关键差异。

理解连接结构的作用，比如邻接矩阵（或类似的结构，如边索引张量），对于掌握先进的图形机器学习技术（如图神经网络 GNNs）背后的核心思想至关重要。为了建立对邻接矩阵在 GNN 中作用的直觉，您可以阅读以下文章：
