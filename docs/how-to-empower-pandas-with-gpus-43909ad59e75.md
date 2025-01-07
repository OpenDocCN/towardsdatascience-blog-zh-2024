# 如何通过 GPU 强化 Pandas

> 原文：[`towardsdatascience.com/how-to-empower-pandas-with-gpus-43909ad59e75?source=collection_archive---------3-----------------------#2024-04-07`](https://towardsdatascience.com/how-to-empower-pandas-with-gpus-43909ad59e75?source=collection_archive---------3-----------------------#2024-04-07)

## 数据科学

## 简要介绍 cuDF，一个 NVIDIA 框架，用于加速 Pandas

[](https://tamimi-naser.medium.com/?source=post_page---byline--43909ad59e75--------------------------------)![Naser Tamimi](https://tamimi-naser.medium.com/?source=post_page---byline--43909ad59e75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--43909ad59e75--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43909ad59e75--------------------------------) [Naser Tamimi](https://tamimi-naser.medium.com/?source=post_page---byline--43909ad59e75--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43909ad59e75--------------------------------) ·6 分钟阅读·2024 年 4 月 7 日

--

![](img/b6627256185bd4f50aba530070176b5e.png)

图片由 [BoliviaInteligente](https://unsplash.com/@boliviainteligente?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Pandas 依然是数据分析和机器学习领域中不可或缺的工具，提供了广泛的功能用于数据读取、转换、清理和写入等任务。然而，它在处理大数据集时的效率有所限制，这使得它在生产环境中的应用或构建强健的数据管道时存在障碍，尽管它在数据科学项目中得到了广泛的使用。

类似于 Apache Spark，Pandas 将数据加载到内存中进行计算和转换。但与 Spark 不同，Pandas 并不是一个分布式计算平台，因此所有操作都必须在单个系统的 CPU 和内存上进行（单节点处理）。这一特点限制了 Pandas 在两个方面的使用：

1.  在单个系统上，Pandas 无法处理大量数据。

1.  即使是能够适应单个系统内存的数据，处理相对较小的数据集也可能需要相当长的时间。

# **Pandas 升级版**

第一个问题可以通过像 Dask 这样的框架来解决。Dask DataFrame 通过在分布式计算机集群上并行化 Pandas，帮助你处理大规模的表格数据。从许多方面来看……
