# 使用 Tablib 处理 Python 中的简单表格数据

> 原文：[https://towardsdatascience.com/use-tablib-to-handle-simple-tabular-data-in-python-fa9e6f0af37f?source=collection_archive---------2-----------------------#2024-11-27](https://towardsdatascience.com/use-tablib-to-handle-simple-tabular-data-in-python-fa9e6f0af37f?source=collection_archive---------2-----------------------#2024-11-27)

## 有时候，浅层抽象比性能更有价值

[](https://medium.com/@ebbeberge?source=post_page---byline--fa9e6f0af37f--------------------------------)[![Eirik Berge, PhD](../Images/7507374e75980fd0c1056af3cd299eaa.png)](https://medium.com/@ebbeberge?source=post_page---byline--fa9e6f0af37f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fa9e6f0af37f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fa9e6f0af37f--------------------------------) [Eirik Berge, PhD](https://medium.com/@ebbeberge?source=post_page---byline--fa9e6f0af37f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fa9e6f0af37f--------------------------------) ·阅读时间 11 分钟·2024年11月27日

--

![](../Images/abefa0b7dab0a1786c309f52f3c65b48.png)

图片由[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 概述

1.  [简介——什么是 Tablib？](#a6cb)

1.  [处理数据集](#969a)

1.  [导入数据](#f09e)

1.  [导出数据](#ca94)

1.  [动态列](#cd4f)

1.  [格式化工具](#dea7)

1.  [总结](#c801)

# 简介——什么是 Tablib？

多年来，我一直在使用 Pandas 和 PySpark 等工具进行数据导入、处理和导出。这些工具非常适合复杂的数据转换和大数据量（当数据适合内存时，Pandas）。然而，我通常会在以下情况下使用这些工具：

+   数据量相对较小。考虑到数据行数远低于100,000行。

+   性能不是问题。考虑到一个偶发任务，或者是每天午夜重复执行的任务，我不在乎它是需要20秒还是5分钟。

+   不需要复杂的转换。可以简单地将20个格式相同的JSON文件导入，将它们堆叠在一起，然后导出为CSV文件。
