# PySpark详解：Delta表

> 原文：[https://towardsdatascience.com/pyspark-explained-delta-tables-dc20916f9422?source=collection_archive---------1-----------------------#2024-08-03](https://towardsdatascience.com/pyspark-explained-delta-tables-dc20916f9422?source=collection_archive---------1-----------------------#2024-08-03)

![](../Images/bae84d4ee8032893a0948da08a6899c8.png)

图片由AI（Ideogram）生成

## 学习如何使用Delta Lake的构建模块。

[](https://medium.com/@thomas_reid?source=post_page---byline--dc20916f9422--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--dc20916f9422--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc20916f9422--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dc20916f9422--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--dc20916f9422--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc20916f9422--------------------------------) ·13分钟阅读·2024年8月3日

--

Delta表是Delta Lake的关键组件，Delta Lake是一个开源存储层，能够为大数据工作负载提供ACID（原子性、一致性、隔离性、持久性）事务支持。

Delta表的概念和实现（以及与之关联的Delta Lake）是由Databricks团队完成的，Databricks是创建Spark的公司。

Databricks现在是一个基于云的数据工程、机器学习和分析平台，围绕Apache Spark构建，提供一个统一的环境用于处理大数据工作负载。Delta表是该环境中的关键组件。

来自AWS背景的我，Delta表让我想起了AWS的Athena服务，Athena使你能够对存储在S3上的文件执行SQL SELECT操作，S3是AWS的大规模存储服务。

不过，有一个关键区别。Athena是一个仅用于查询的工具，而Delta表不仅允许你查询数据，还可以轻松地更新、删除和插入数据记录。在这一点上，Delta表更像是Apache Iceberg格式的表格。但它们相比Iceberg表的优势在于，它们与Spark生态系统的集成更加紧密。
