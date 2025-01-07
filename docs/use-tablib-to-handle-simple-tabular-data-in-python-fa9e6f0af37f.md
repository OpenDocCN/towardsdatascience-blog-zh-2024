# 使用 Tablib 处理 Python 中的简单表格数据

> 原文：[`towardsdatascience.com/use-tablib-to-handle-simple-tabular-data-in-python-fa9e6f0af37f?source=collection_archive---------2-----------------------#2024-11-27`](https://towardsdatascience.com/use-tablib-to-handle-simple-tabular-data-in-python-fa9e6f0af37f?source=collection_archive---------2-----------------------#2024-11-27)

## 有时候，浅层抽象比性能更有价值

[](https://medium.com/@ebbeberge?source=post_page---byline--fa9e6f0af37f--------------------------------)![Eirik Berge, PhD](https://medium.com/@ebbeberge?source=post_page---byline--fa9e6f0af37f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fa9e6f0af37f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fa9e6f0af37f--------------------------------) [Eirik Berge, PhD](https://medium.com/@ebbeberge?source=post_page---byline--fa9e6f0af37f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fa9e6f0af37f--------------------------------) ·阅读时间 11 分钟·2024 年 11 月 27 日

--

![](img/abefa0b7dab0a1786c309f52f3c65b48.png)

图片由[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 概述

1.  简介——什么是 Tablib？

1.  处理数据集

1.  导入数据

1.  导出数据

1.  动态列

1.  格式化工具

1.  总结

# 简介——什么是 Tablib？

多年来，我一直在使用 Pandas 和 PySpark 等工具进行数据导入、处理和导出。这些工具非常适合复杂的数据转换和大数据量（当数据适合内存时，Pandas）。然而，我通常会在以下情况下使用这些工具：

+   数据量相对较小。考虑到数据行数远低于 100,000 行。

+   性能不是问题。考虑到一个偶发任务，或者是每天午夜重复执行的任务，我不在乎它是需要 20 秒还是 5 分钟。

+   不需要复杂的转换。可以简单地将 20 个格式相同的 JSON 文件导入，将它们堆叠在一起，然后导出为 CSV 文件。
