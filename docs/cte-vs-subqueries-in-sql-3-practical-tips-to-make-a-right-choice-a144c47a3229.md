# SQL 中的 CTE 与子查询——3 个实用技巧帮助做出正确选择

> 原文：[`towardsdatascience.com/cte-vs-subqueries-in-sql-3-practical-tips-to-make-a-right-choice-a144c47a3229?source=collection_archive---------3-----------------------#2024-09-16`](https://towardsdatascience.com/cte-vs-subqueries-in-sql-3-practical-tips-to-make-a-right-choice-a144c47a3229?source=collection_archive---------3-----------------------#2024-09-16)

## 数据科学

## 掌握 CTE 和子查询之间的关键区别，准确了解何时以及如何使用它们！

[](https://medium.com/@17.rsuraj?source=post_page---byline--a144c47a3229--------------------------------)![Suraj Gurav](https://medium.com/@17.rsuraj?source=post_page---byline--a144c47a3229--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a144c47a3229--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a144c47a3229--------------------------------) [Suraj Gurav](https://medium.com/@17.rsuraj?source=post_page---byline--a144c47a3229--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a144c47a3229--------------------------------) ·6 分钟阅读·2024 年 9 月 16 日

--

![](img/fe4f7cb942a0366cc6e26d98e1abdcc9.png)

照片由作者拍摄

我该在 SQL 中使用 CTE 还是子查询？——你一定经常会问这个问题。

我也有过这样的困惑！

子查询，顾名思义，是在另一个查询内部的查询，而 CTE 或称公共表表达式则是一个临时结果集，你需要单独定义。无论是子查询还是 CTE，都可以像使用简单的筛选一样简单，也可以像进行复杂的数据转换一样复杂。

而这种相似性常常是关于为什么以及何时使用它们的混淆源。

当我刚开始接触数据科学时，我总是很难做出这个选择。然而，随着时间的推移，我意识到，这其实不在于“更好”，而是**找到合适的方法来完成任务**。

例如，当我在一个逻辑复杂的项目中工作，需要分解数据转换或处理层次数据时——我会使用 CTE。但当我需要进行一次性计算或筛选时，我会使用子查询。

关于 CTE 的详细讨论，我强烈推荐阅读——
