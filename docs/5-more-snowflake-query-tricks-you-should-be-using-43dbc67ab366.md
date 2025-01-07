# 5个你应该使用的Snowflake查询技巧

> 原文：[https://towardsdatascience.com/5-more-snowflake-query-tricks-you-should-be-using-43dbc67ab366?source=collection_archive---------9-----------------------#2024-01-08](https://towardsdatascience.com/5-more-snowflake-query-tricks-you-should-be-using-43dbc67ab366?source=collection_archive---------9-----------------------#2024-01-08)

## 更少的行数，和更快的执行时间，还有一些NLP

[](https://medium.com/@anthonyli358?source=post_page---byline--43dbc67ab366--------------------------------)[![Anthony Li](../Images/531f93cc6080694b60bf160519274eab.png)](https://medium.com/@anthonyli358?source=post_page---byline--43dbc67ab366--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--43dbc67ab366--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--43dbc67ab366--------------------------------) [Anthony Li](https://medium.com/@anthonyli358?source=post_page---byline--43dbc67ab366--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43dbc67ab366--------------------------------) ·阅读时长5分钟·2024年1月8日

--

![](../Images/f8ca0f5a761e423f254731e1e2f5328b.png)

照片来自[凯莉·西克马](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Snowflake是一个云计算数据解决方案，允许用户在其云平台上存储数据并直接运行查询，可以通过网页浏览器直接访问。它常用于数据存储和自动扩展功能，其中集群会自动启动和停止，以管理查询负载。

在[本系列的第一部分，](https://medium.com/towards-data-science/5-snowflake-query-tricks-you-arent-using-but-should-be-7f264b2a72d8)我们探讨了Snowflake如何具有独特的查询语法，而这些语法在其他数据库系统如PostgreSQL或MySQL中可能无法找到。当正确使用这些子句时，它们有助于提高语法和可读性，最重要的是，减少计算成本和执行时间。

在下文中，我们将介绍5个这样的子句，包括一些字符串处理函数，您可能最初会认为这些需要使用Python或R来完成。

# 1\. MAX_BY / MIN_BY

[min_by](https://docs.snowflake.com/en/sql-reference/functions/min_by)和对应的max_by子句允许我们找到与给定列的最小/最大值对应的行，并返回该行中另一个列的值。

一个常见的方法是先在子查询中找到最小值，然后将其作为筛选条件。例如…
