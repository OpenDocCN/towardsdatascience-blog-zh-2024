# 空间索引：空间填充曲线

> 原文：[https://towardsdatascience.com/spatial-index-space-filling-curves-e67baec2186a?source=collection_archive---------18-----------------------#2024-06-11](https://towardsdatascience.com/spatial-index-space-filling-curves-e67baec2186a?source=collection_archive---------18-----------------------#2024-06-11)

## 多维数据的空间索引与空间填充曲线

[](https://pyblog.medium.com/?source=post_page---byline--e67baec2186a--------------------------------)[![Adesh Nalpet Adimurthy](../Images/fb3e0c4d3c3f829fd95bde08f0a4cd57.png)](https://pyblog.medium.com/?source=post_page---byline--e67baec2186a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e67baec2186a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e67baec2186a--------------------------------) [Adesh Nalpet Adimurthy](https://pyblog.medium.com/?source=post_page---byline--e67baec2186a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e67baec2186a--------------------------------) ·12分钟阅读·2024年6月11日

--

![](../Images/0af40f68c84b167d3b27e1e01254c6a2.png)

# 0. 概述

空间数据由于网络服务跟踪用户在何时何地进行操作而迅速增长（/正在增长）。大多数应用程序都会添加位置标签，并且通常允许用户在特定的地点和时间进行签到。这一增长主要归因于智能手机，它们充当了位置传感器，使得捕捉和分析此类数据变得前所未有的容易。

本文的目标是为多维索引的需求奠定基础，并深入探讨广泛应用于关系型和非关系型数据库中的空间填充曲线在空间索引中的使用。我们将分析每种类型的优缺点，并讨论当今最流行的索引类型。

![](../Images/9f0e5eab06bd7ce6e04dac2f4877ba1d.png)

空间索引分为两大类：空间驱动结构和数据驱动结构。数据驱动结构，如R树家族，是根据数据本身的分布量身定制的。空间驱动结构包括划分树（kd树、四叉树）、空间填充曲线（Z-order、Hilbert）和网格系统（H3、S2、Geohash），每种…
