# 5 个示例，助你掌握 PySpark 窗口操作

> 原文：[`towardsdatascience.com/5-examples-to-master-pyspark-window-operations-26583066e227?source=collection_archive---------3-----------------------#2024-01-22`](https://towardsdatascience.com/5-examples-to-master-pyspark-window-operations-26583066e227?source=collection_archive---------3-----------------------#2024-01-22)

## 数据分析必备工具

[](https://sonery.medium.com/?source=post_page---byline--26583066e227--------------------------------)![Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--26583066e227--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--26583066e227--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--26583066e227--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--26583066e227--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--26583066e227--------------------------------) ·8 分钟阅读·2024 年 1 月 22 日

--

![](img/b555efeea498ae8a2fe5917442513e28.png)

图片来源：[Pierre Châtel-Innocenti](https://unsplash.com/@chatelp?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于[Unsplash](https://unsplash.com/photos/white-and-beige-building-windows-F4VHOj76D0o?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

我所使用过的所有数据分析和处理工具都具备窗口操作功能。有些工具更加灵活和强大，但能够在窗口上进行计算是数据分析中必须掌握的技能。

什么是数据分析中的窗口？

窗口是一组在某些方面相关的行。这个关系可以是属于同一组，或者是连续 n 天的记录。一旦我们根据所需的约束生成了窗口，就可以在其上进行计算或聚合操作。

在本文中，我们将通过 5 个详细的示例，全面了解 PySpark 中的窗口操作。我们将学习如何使用分区创建窗口，自定义这些窗口，并进行相关的计算。

PySpark 是 Spark 的 Python API，Spark 是一个用于大规模数据处理的分析引擎。

# 数据

我为本文准备了一个包含虚拟数据的示例数据集，您可以从我的[datasets](https://github.com/SonerYldrm/datasets/tree/main)仓库下载。我们在本文中使用的数据集名为“sample_sales_pyspark.csv”。

让我们启动一个 Spark 会话并从这个数据集中创建一个 DataFrame。

```py
from pyspark.sql import SparkSession…
```
