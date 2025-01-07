# PySpark 解释：Delta 表的时间旅行查询

> 原文：[`towardsdatascience.com/pyspark-explained-delta-table-time-travel-queries-84d074c9db35?source=collection_archive---------9-----------------------#2024-08-29`](https://towardsdatascience.com/pyspark-explained-delta-table-time-travel-queries-84d074c9db35?source=collection_archive---------9-----------------------#2024-08-29)

![](img/ed2161ceca5356eb6e6ccf6b25398bc0.png)

图片来源：AI（Dalle-3）

## 比时间领主还快地回顾过去。

## 删除、恢复和重放历史数据事务

[](https://medium.com/@thomas_reid?source=post_page---byline--84d074c9db35--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--84d074c9db35--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--84d074c9db35--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--84d074c9db35--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--84d074c9db35--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--84d074c9db35--------------------------------) ·15 分钟阅读·2024 年 8 月 29 日

--

在之前的文章中，我深入探讨了 Databricks Delta 表，它们是什么以及如何使用它们。可以查看下面的链接，

[](/pyspark-explained-delta-tables-dc20916f9422?source=post_page-----84d074c9db35--------------------------------) ## PySpark 解释：Delta 表

### 学习如何使用 Delta Lake 的基本构件。

towardsdatascience.com

我在那篇文章中提到的一个优势是可以对 Delta 表进行时间旅行查询。换句话说，你可以回溯并查看过去任何时间点某个特定表中的数据。

为什么这可能有用呢？我能想到一些优势，尤其是一个非常重要的。

**1\. 数据审计和合规性**

+   **历史数据检查** 时间旅行允许你访问数据的历史版本，这对于审计非常有用。你可以检查数据的过去状态，以确保符合监管要求。

**2\. 调试和开发**

+   **调试数据问题** 通过查询以前的版本来解决……
