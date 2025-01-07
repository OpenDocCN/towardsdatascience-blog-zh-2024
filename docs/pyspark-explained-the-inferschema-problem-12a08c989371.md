# PySpark 解析：InferSchema 问题

> 原文：[https://towardsdatascience.com/pyspark-explained-the-inferschema-problem-12a08c989371?source=collection_archive---------8-----------------------#2024-09-16](https://towardsdatascience.com/pyspark-explained-the-inferschema-problem-12a08c989371?source=collection_archive---------8-----------------------#2024-09-16)

![](../Images/7fee21d875d5afa38e089f8af407ea74.png)

图像来自 AI（Dalle-3）

## 在阅读大型 CSV 文件时，使用此常见选项之前请三思

[](https://medium.com/@thomas_reid?source=post_page---byline--12a08c989371--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--12a08c989371--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--12a08c989371--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--12a08c989371--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--12a08c989371--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12a08c989371--------------------------------) ·10分钟阅读·2024年9月16日

--

无论你是数据科学家、数据工程师，还是程序员，阅读和处理 CSV 数据将是你多年来的基础技能之一。

大多数编程语言都可以原生地或通过库来读取和写入 CSV 数据文件，PySpark 也不例外。

它提供了一个非常有用的`**spark.read**`函数。你可能已经多次使用过这个函数，并且配合它的`**inferSchema**`指令使用。事实上，使用频繁到几乎成了习惯。

如果这正是你的情况，那么在本文中，我希望说服你，从性能角度来看，通常在读取大型 CSV 文件时使用这个选项是一个糟糕的选择，我将展示你可以改做什么。

首先，我们应当检查 inferSchema 在何时何地使用，以及它为何如此流行。

何时何地使用是很简单的。当读取 CSV 文件到 Spark DataFrame 时，inferSchema 会作为选项显式地用于**spark.read**函数。

你可能会问，“其他类型的文件呢？”

Parquet 和 ORC 数据文件的模式已存储在文件中。因此，不需要显式的模式推断。
