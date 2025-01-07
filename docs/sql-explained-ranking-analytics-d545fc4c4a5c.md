# SQL 解析：排名分析

> 原文：[https://towardsdatascience.com/sql-explained-ranking-analytics-d545fc4c4a5c?source=collection_archive---------13-----------------------#2024-06-11](https://towardsdatascience.com/sql-explained-ranking-analytics-d545fc4c4a5c?source=collection_archive---------13-----------------------#2024-06-11)

![](../Images/7b3e9d3a4d02e1ad400006c9a00283ab.png)

图片来源：AI（Dalle-3）

## 它们是什么，以及如何使用它们

[](https://medium.com/@thomas_reid?source=post_page---byline--d545fc4c4a5c--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--d545fc4c4a5c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d545fc4c4a5c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d545fc4c4a5c--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--d545fc4c4a5c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d545fc4c4a5c--------------------------------) ·13分钟阅读·2024年6月11日

--

如果你有使用过像 Oracle、SQL Server、Postgres 等流行的关系型数据库管理系统（RDBMS）的经验，你可能在某个阶段遇到过分析函数，有时也叫窗口函数。

分析功能允许你计算数据集中每组行的聚合和排名序列。如果你曾经怀疑是否值得投入一些时间来了解和使用分析函数，我可以告诉你，答案是**肯定的**。它们非常有用，能够让 SQL 执行一些没有它们就难以甚至不可能完成的任务。

在本文中，我们将专注于一种特定类别的分析——排名函数，并通过分析 SQL 中四种最常用的排名技术来进行讲解。我们将解释它们是什么，并提供使用示例。

## 排名分析语法

大多数现代 SQL 方言中，***排名***分析函数的一般格式是：

> rank | dense_rank | row_number() | ntile
> 
> over(partition_by_clause order_by_clause windowing_clause)

让我们简要地依次查看上面语句的每个部分。
