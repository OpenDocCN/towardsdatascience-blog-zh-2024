# SQL解释：公共表表达式

> 原文：[https://towardsdatascience.com/sql-explained-common-table-expressions-fc23e4675890?source=collection_archive---------3-----------------------#2024-05-22](https://towardsdatascience.com/sql-explained-common-table-expressions-fc23e4675890?source=collection_archive---------3-----------------------#2024-05-22)

![](../Images/feb13ea203eef5edd5fee914cb37aac9.png)

图片来自AI（Dalle-3）

## 什么是CTE，它们如何使用

[](https://medium.com/@thomas_reid?source=post_page---byline--fc23e4675890--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--fc23e4675890--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc23e4675890--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fc23e4675890--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--fc23e4675890--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc23e4675890--------------------------------) ·阅读时间：8分钟·2024年5月22日

--

SQL中的公共表表达式（或简称CTE）是临时的、命名的结果集，包含从另一个SQL查询派生的中间数据。一旦你在CTE中有了数据，你可以在同一查询中多次引用这些数据。

根据上述描述，你可能会认为CTE听起来像SQL中的常规临时表，从某些方面来看，它们确实相似。那么，为什么要使用CTE而不是临时表呢？为了回答这个问题，我们需要了解临时表的两个主要缺点。

其中一个缺点是临时表可能导致代码更加复杂，尤其是在大型SQL脚本的不同部分之间使用时。它们需要明确的创建、删除，可能还需要为其创建索引，这增加了SQL和会话管理的开销。

其次，临时表会消耗物理存储空间，如果空间紧张且有大量临时表时，这可能是一个需要考虑的因素。此外，当查看使用临时表的查询时，可能不清楚临时表中包含了哪些数据以及这些数据来自何处。

公共表表达式（CTE）没有上述问题。首先，它们是短暂的，一旦SQL会话结束，CTE就会超出作用域，任何...
