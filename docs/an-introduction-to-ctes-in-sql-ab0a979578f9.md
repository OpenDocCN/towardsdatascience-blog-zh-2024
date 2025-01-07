# SQL中的公共表表达式简介

> 原文：[https://towardsdatascience.com/an-introduction-to-ctes-in-sql-ab0a979578f9?source=collection_archive---------4-----------------------#2024-12-04](https://towardsdatascience.com/an-introduction-to-ctes-in-sql-ab0a979578f9?source=collection_archive---------4-----------------------#2024-12-04)

## 探索公共表表达式（CTE）如何帮助优化SQL性能和可读性

[](https://eugenia-anello.medium.com/?source=post_page---byline--ab0a979578f9--------------------------------)[![Eugenia Anello](../Images/537f444252cdc60709e7a19e37734c7b.png)](https://eugenia-anello.medium.com/?source=post_page---byline--ab0a979578f9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ab0a979578f9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ab0a979578f9--------------------------------) [Eugenia Anello](https://eugenia-anello.medium.com/?source=post_page---byline--ab0a979578f9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ab0a979578f9--------------------------------) ·阅读时长：6分钟·2024年12月4日

--

![](../Images/b1164234684320dc0eae942a23b4d9dc.png)

图片来源：[SpaceX](https://unsplash.com/it/@spacex) via [Unsplash](https://unsplash.com/it/foto/fotografia-di-navi-spaziali-uj3hvdfQujI)

在过去的几个月里，我深刻认识到编写干净、可读且高效的SQL查询的重要性。它们对于从不同表中整合信息至关重要。

然而，从头开始编写复杂查询可能非常耗时，尤其是在你经常使用它们来分析数据时。为了应对这种需求，SQL提供了一个强大的结构，称为公共表表达式（CTE）。

在这篇文章中，我将解释什么是公共表表达式，它为什么有用，并通过示例展示它的应用。让我们开始吧！

**目录：**

+   **什么是公共表表达式（CTE）？**

+   **设置DBeaver和数据库**

+   **CTE的三个简单示例**

**什么是公共表表达式（CTE）？**

公共表表达式（CTE）是一个临时结果集，它通过将复杂查询拆分成更小、更易管理的部分，从而简化查询，使其更具可读性和可维护性。它通过将复杂查询分解为顺序的模块化子查询来工作。它不仅仅局限于管理复杂查询，它还可以…
