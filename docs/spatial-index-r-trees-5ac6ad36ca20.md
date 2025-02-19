# 空间索引：R 树

> 原文：[`towardsdatascience.com/spatial-index-r-trees-5ac6ad36ca20?source=collection_archive---------10-----------------------#2024-07-30`](https://towardsdatascience.com/spatial-index-r-trees-5ac6ad36ca20?source=collection_archive---------10-----------------------#2024-07-30)

## 用于空间索引的数据驱动结构

[](https://pyblog.medium.com/?source=post_page---byline--5ac6ad36ca20--------------------------------)![Adesh Nalpet Adimurthy](https://pyblog.medium.com/?source=post_page---byline--5ac6ad36ca20--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ac6ad36ca20--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ac6ad36ca20--------------------------------) [Adesh Nalpet Adimurthy](https://pyblog.medium.com/?source=post_page---byline--5ac6ad36ca20--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ac6ad36ca20--------------------------------) ·阅读时间 10 分钟·2024 年 7 月 30 日

--

![](img/abeb7b52a23abe7ab7f0800c86d16428.png)

如果你一直关注[空间索引系列](https://pyblog.medium.com/list/index-express-a3c24005a27a)，它从多维索引的需求开始，并介绍了[空间填充曲线](https://medium.com/towards-data-science/spatial-index-space-filling-curves-e67baec2186a)，然后深入探讨了[网格系统](https://medium.com/towards-data-science/spatial-index-grid-systems-f02880fb147a)（GeoHash 和 Google S2）和[镶嵌](https://medium.com/towards-data-science/spatial-index-tessellation-aca39463fe9f)（Uber H3）。

在这篇文章中，我们将探讨[R 树](https://en.wikipedia.org/wiki/R-tree)数据结构（数据驱动结构），它广泛用于存储多维数据，例如数据点、线段和矩形。

# 1\. R 树和矩形

例如，考虑下面的大学布局计划。我们可以使用 R-Tree 数据结构对地图上的建筑物进行索引。

为此，我们可以在建筑物或一组建筑物周围放置矩形，然后对其进行索引。假设有一个更大的地图区域代表一个更大的部门，并且我们需要查询该部门内的所有建筑物。我们可以使用 R-Tree 来查找所有位于（部分或完全包含在）更大区域内的建筑物（查询矩形）。

![](img/6ab25a4b6aab59f91f4f6148401fa794.png)

在上图中，红色矩形表示查询矩形，用于询问...
