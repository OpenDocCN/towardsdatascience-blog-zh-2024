# PySpark 解析：创建和填充 DataFrame 的四种方式

> 原文：[`towardsdatascience.com/pyspark-explained-four-ways-to-create-and-populate-dataframes-31f3e4322ad9?source=collection_archive---------3-----------------------#2024-07-04`](https://towardsdatascience.com/pyspark-explained-four-ways-to-create-and-populate-dataframes-31f3e4322ad9?source=collection_archive---------3-----------------------#2024-07-04)

![](img/49e4e3686f9183a0297651c636080f94.png)

图像由 AI（Dalle-3）生成

## 从 CSV 到数据库：将数据加载到 PySpark DataFrame 中

[](https://medium.com/@thomas_reid?source=post_page---byline--31f3e4322ad9--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--31f3e4322ad9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--31f3e4322ad9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31f3e4322ad9--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--31f3e4322ad9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31f3e4322ad9--------------------------------) ·11 分钟阅读·2024 年 7 月 4 日

--

在使用 PySpark 时，特别是如果你有 SQL 背景，首先你可能想做的事情就是将你想处理的数据放入 DataFrame。一旦数据在 DataFrame 中，就可以很容易地从 DataFrame 创建一个临时视图（或永久表）。在这个阶段，PySpark SQL 提供的丰富操作集就可以供你使用，帮助你进一步探索和处理数据。

由于许多标准的 SQL 技能可以轻松转移到 PySpark SQL，因此在处理管道中尽早准备数据以直接使用 PySpark SQL 至关重要。这样做应该是高效数据处理和分析的首要任务。

当然，你不***一定***非得这么做，因为你可以使用 PySpark SQL 对视图或表执行的所有操作也可以直接在 DataFrame 上通过 API 完成。但作为一个对使用 SQL 比对使用 DataFrame API 更加熟悉的人，我在使用 Spark 时的首选流程一直是，

> **输入数据 -> DataFrame -> 临时视图 -> SQL 处理**

为了帮助你完成这个过程，本文将讨论这个管道的第一部分，即将数据放入 DataFrame，并展示四种方法……
