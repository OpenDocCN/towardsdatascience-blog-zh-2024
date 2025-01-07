# 图神经网络 第一部分：图卷积网络解析

> 原文：[`towardsdatascience.com/graph-neural-networks-part-1-graph-convolutional-networks-explained-9c6aaa8a406e?source=collection_archive---------4-----------------------#2024-10-01`](https://towardsdatascience.com/graph-neural-networks-part-1-graph-convolutional-networks-explained-9c6aaa8a406e?source=collection_archive---------4-----------------------#2024-10-01)

![](img/013fac9f1e77ff6821b3594223c6bf5b.png)

该图片由作者使用 Dall·E 创建。

## 图节点分类与图卷积网络

[](https://hennie-de-harder.medium.com/?source=post_page---byline--9c6aaa8a406e--------------------------------)![Hennie de Harder](https://hennie-de-harder.medium.com/?source=post_page---byline--9c6aaa8a406e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9c6aaa8a406e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9c6aaa8a406e--------------------------------) [Hennie de Harder](https://hennie-de-harder.medium.com/?source=post_page---byline--9c6aaa8a406e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9c6aaa8a406e--------------------------------) ·阅读时间 11 分钟·2024 年 10 月 1 日

--

**数据并不总是整齐地适应行和列。实际上，数据通常遵循图结构，例如社交网络、蛋白质结构、推荐系统或交通运输系统。忽略图拓扑结构中的信息可能会大幅降低机器学习模型的表现。幸运的是，有一种方法可以将这些信息纳入模型。**

图神经网络（GNNs）旨在从以节点和边表示的数据中学习。随着时间的推移，GNNs 不断发展，在这篇文章中你将了解图卷积网络（GCNs）。我的下一篇文章将介绍图注意力网络（GATs）。GCNs 和 GATs 是当前最先进的模型所基于的两种基本架构，因此如果你想学习关于 GNNs 的知识，这是一个不错的起点。让我们开始吧！

> 对图形不熟悉？[这篇](https://medium.com/towards-data-science/optimizing-connections-mathematical-optimization-within-graphs-7364e082a984)文章的第一部分（图基础）解释了图形的基础知识。此外，你还应该了解[神经网络](https://en.wikipedia.org/wiki/Neural_network_(machine_learning))（本文章的“数据集与前提”部分提供了简短的回顾）。
