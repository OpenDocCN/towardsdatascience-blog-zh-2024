# PySpark 详解：Delta 表

> 原文：[`towardsdatascience.com/pyspark-explained-delta-tables-dc20916f9422?source=collection_archive---------1-----------------------#2024-08-03`](https://towardsdatascience.com/pyspark-explained-delta-tables-dc20916f9422?source=collection_archive---------1-----------------------#2024-08-03)

![](img/bae84d4ee8032893a0948da08a6899c8.png)

图片由 AI（Ideogram）生成

## 学习如何使用 Delta Lake 的构建模块。

[](https://medium.com/@thomas_reid?source=post_page---byline--dc20916f9422--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--dc20916f9422--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc20916f9422--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc20916f9422--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--dc20916f9422--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc20916f9422--------------------------------) ·13 分钟阅读·2024 年 8 月 3 日

--

Delta 表是 Delta Lake 的关键组件，Delta Lake 是一个开源存储层，能够为大数据工作负载提供 ACID（原子性、一致性、隔离性、持久性）事务支持。

Delta 表的概念和实现（以及与之关联的 Delta Lake）是由 Databricks 团队完成的，Databricks 是创建 Spark 的公司。

Databricks 现在是一个基于云的数据工程、机器学习和分析平台，围绕 Apache Spark 构建，提供一个统一的环境用于处理大数据工作负载。Delta 表是该环境中的关键组件。

来自 AWS 背景的我，Delta 表让我想起了 AWS 的 Athena 服务，Athena 使你能够对存储在 S3 上的文件执行 SQL SELECT 操作，S3 是 AWS 的大规模存储服务。

不过，有一个关键区别。Athena 是一个仅用于查询的工具，而 Delta 表不仅允许你查询数据，还可以轻松地更新、删除和插入数据记录。在这一点上，Delta 表更像是 Apache Iceberg 格式的表格。但它们相比 Iceberg 表的优势在于，它们与 Spark 生态系统的集成更加紧密。
