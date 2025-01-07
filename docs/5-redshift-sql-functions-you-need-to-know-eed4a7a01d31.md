# 你需要知道的 5 个 Redshift SQL 函数

> 原文：[`towardsdatascience.com/5-redshift-sql-functions-you-need-to-know-eed4a7a01d31?source=collection_archive---------11-----------------------#2024-03-08`](https://towardsdatascience.com/5-redshift-sql-functions-you-need-to-know-eed4a7a01d31?source=collection_archive---------11-----------------------#2024-03-08)

## 包括如何使用这些函数的代码示例

[](https://madison-schott.medium.com/?source=post_page---byline--eed4a7a01d31--------------------------------)![Madison Schott](https://madison-schott.medium.com/?source=post_page---byline--eed4a7a01d31--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--eed4a7a01d31--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eed4a7a01d31--------------------------------) [Madison Schott](https://madison-schott.medium.com/?source=post_page---byline--eed4a7a01d31--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eed4a7a01d31--------------------------------) ·阅读时间：5 分钟·2024 年 3 月 8 日

--

![](img/e828594d7da7cd85091a16d255f4e294.png)

[Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的照片

如果你是 Redshift 的新用户，你可能会发现 SQL 语法与在其他数据仓库中编写的 SQL 有所不同。

每个数据仓库都有自己特定的 SQL 方言，Redshift 也不例外。

一开始，发现你最喜欢的函数不存在时，可能会感到沮丧。然而，Redshift 有很多你可以在代码中利用的优秀函数。

在这篇文章中，我将向你展示我在工作中发现的最有用的 Redshift 函数。每个函数都包括一个定义和代码示例，说明如何使用它。

# PIVOT

[PIVOT](https://docs.aws.amazon.com/redshift/latest/dg/r_FROM_clause-pivot-unpivot-examples.html)是 Redshift 内置的一个函数，它允许你进行数据透视。我的意思是什么呢？**数据透视允许你重新组织数据，使得行中的值变成列，或列中的值变成行。**

PIVOT 可以帮助你：

+   统计列中的值

+   聚合行值

+   基于列或行值派生布尔字段

我最近在 Redshift 中使用了 PIVOT 来查找不同页面是否对每个用户有效。为此，我需要对`page_type`字段进行 PIVOT 操作，并…
