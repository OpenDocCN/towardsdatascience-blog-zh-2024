# 4 个示例助你提升 PySpark 技能

> 原文：[`towardsdatascience.com/4-examples-to-take-your-pyspark-skills-to-next-level-2a04cbe6e630?source=collection_archive---------6-----------------------#2024-01-30`](https://towardsdatascience.com/4-examples-to-take-your-pyspark-skills-to-next-level-2a04cbe6e630?source=collection_archive---------6-----------------------#2024-01-30)

## 使用 PySpark 适应大规模数据处理

[](https://sonery.medium.com/?source=post_page---byline--2a04cbe6e630--------------------------------)![Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--2a04cbe6e630--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2a04cbe6e630--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2a04cbe6e630--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--2a04cbe6e630--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2a04cbe6e630--------------------------------) ·7 分钟阅读·2024 年 1 月 30 日

--

![](img/a222122f8db4bec4e2342525412c2088.png)

图片由 [fabio](https://unsplash.com/@fabioha?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/geometric-shape-digital-wallpaper-oyXis2kALVg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

PySpark 是 Spark 的 Python API，Spark 是一个用于大规模数据处理的分析引擎。特别是当我们处理那些使用 Pandas 和 SQL 等工具难以应对的大型数据集时，Spark 已成为数据科学生态系统中占主导地位的工具。

在本文中，我们将学习 PySpark，但与大多数教程的视角不同。我们不会简单地讲解常用的 PySpark 函数并说明如何使用它们，而是通过解决一些具有挑战的数据清洗和处理任务来学习。这样的学习方式不仅帮助我们学习 PySpark 函数，还能帮助我们了解何时使用它们。

在我们开始示例之前，先让我告诉你如何获取示例中使用的数据集。它是我用虚拟数据准备的一个示例数据集。你可以从我的 [datasets](https://github.com/SonerYldrm/datasets/tree/main) 仓库下载，文件名是“sample_sales_pyspark.csv”。

让我们从这个数据集创建一个 DataFrame 开始。

```py
from pyspark.sql import SparkSession
from pyspark.sql import Window, functions as F

spark = SparkSession.builder.getOrCreate()

data = spark.read.csv("sample_sales_pyspark.csv", header=True)

data.show(5)
# output…
```
