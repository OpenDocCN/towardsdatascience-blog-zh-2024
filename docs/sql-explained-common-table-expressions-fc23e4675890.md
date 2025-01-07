# SQL 解释：公共表表达式

> 原文：[`towardsdatascience.com/sql-explained-common-table-expressions-fc23e4675890?source=collection_archive---------3-----------------------#2024-05-22`](https://towardsdatascience.com/sql-explained-common-table-expressions-fc23e4675890?source=collection_archive---------3-----------------------#2024-05-22)

![](img/feb13ea203eef5edd5fee914cb37aac9.png)

图片来自 AI（Dalle-3）

## 什么是 CTE，它们如何使用

[](https://medium.com/@thomas_reid?source=post_page---byline--fc23e4675890--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--fc23e4675890--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc23e4675890--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc23e4675890--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--fc23e4675890--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc23e4675890--------------------------------) ·阅读时间：8 分钟·2024 年 5 月 22 日

--

SQL 中的公共表表达式（或简称 CTE）是临时的、命名的结果集，包含从另一个 SQL 查询派生的中间数据。一旦你在 CTE 中有了数据，你可以在同一查询中多次引用这些数据。

根据上述描述，你可能会认为 CTE 听起来像 SQL 中的常规临时表，从某些方面来看，它们确实相似。那么，为什么要使用 CTE 而不是临时表呢？为了回答这个问题，我们需要了解临时表的两个主要缺点。

其中一个缺点是临时表可能导致代码更加复杂，尤其是在大型 SQL 脚本的不同部分之间使用时。它们需要明确的创建、删除，可能还需要为其创建索引，这增加了 SQL 和会话管理的开销。

其次，临时表会消耗物理存储空间，如果空间紧张且有大量临时表时，这可能是一个需要考虑的因素。此外，当查看使用临时表的查询时，可能不清楚临时表中包含了哪些数据以及这些数据来自何处。

公共表表达式（CTE）没有上述问题。首先，它们是短暂的，一旦 SQL 会话结束，CTE 就会超出作用域，任何...
