# 来自图论的Floyd-Warshall算法，应用于解析分子结构

> 原文：[https://towardsdatascience.com/the-floyd-warshall-algorithm-from-graph-theory-applied-to-parsing-molecular-structures-39f8c99c9fe1?source=collection_archive---------9-----------------------#2024-08-22](https://towardsdatascience.com/the-floyd-warshall-algorithm-from-graph-theory-applied-to-parsing-molecular-structures-39f8c99c9fe1?source=collection_archive---------9-----------------------#2024-08-22)

## 通过简单的JavaScript代码提供的实践性解释

[](https://lucianosphere.medium.com/?source=post_page---byline--39f8c99c9fe1--------------------------------)[![LucianoSphere (Luciano Abriata, PhD)](../Images/a8ae3085d094749bbdd1169cca672b86.png)](https://lucianosphere.medium.com/?source=post_page---byline--39f8c99c9fe1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--39f8c99c9fe1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--39f8c99c9fe1--------------------------------) [LucianoSphere (Luciano Abriata, PhD)](https://lucianosphere.medium.com/?source=post_page---byline--39f8c99c9fe1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--39f8c99c9fe1--------------------------------) ·阅读时间：8分钟·2024年8月22日

--

Floyd-Warshall算法在图论中至关重要，因为它提供了一种高效的方法来计算图中所有节点对之间的最短路径。这个经典的动态规划算法不仅在理论网络分析中有传统的应用，而且在其他领域也得到了广泛的应用。它的工作原理是读取一个描述哪些节点对由一条边连接的矩阵，并输出连接每一对节点的最小边数：

![](../Images/21153ae53c61a2201936883662a75eef.png)

一组由边（浅绿色线）连接的节点（红色）以及通过Floyd-Warshall算法计算的两组距离（节点对之间的链接数，深绿色带虚线）。直接链接通过一个称为“邻接矩阵”的矩阵进行编码，该矩阵的元素为1（节点相连）或0（节点不相连）。算法输出的矩阵大小相同，但包含所有节点之间的最小边数，即将任何两个节点分开的最少边数。此图及所有其他图表均由作者制作。

这是关于图中连通性的重要信息，具有广泛的应用；例如，在优化通信网络、分析社交网络中的联系，或者如我在此介绍的那样，在解析分子结构中——这是化学信息学和结构生物信息学许多任务的核心。

在这篇文章中，我将向你展示如何使用弗洛伊德-沃舍尔算法来计算分子中原子之间的键距，换句话说，就是计算分子中每一对原子之间的最小键数。希望我的例子不仅能展示该算法在化学中的应用，还能激发你将其应用于其他领域…
