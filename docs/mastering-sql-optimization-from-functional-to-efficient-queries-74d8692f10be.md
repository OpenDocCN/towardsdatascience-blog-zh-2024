# 掌握 SQL 优化：从功能性到高效的查询

> 原文：[`towardsdatascience.com/mastering-sql-optimization-from-functional-to-efficient-queries-74d8692f10be?source=collection_archive---------0-----------------------#2024-07-01`](https://towardsdatascience.com/mastering-sql-optimization-from-functional-to-efficient-queries-74d8692f10be?source=collection_archive---------0-----------------------#2024-07-01)

## 帮助我每天减少 50 小时 Snowflake 查询时间的六个简单而有效的 SQL 技巧

[](https://ydong029.medium.com/?source=post_page---byline--74d8692f10be--------------------------------)![Yu Dong](https://ydong029.medium.com/?source=post_page---byline--74d8692f10be--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--74d8692f10be--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74d8692f10be--------------------------------) [于东](https://ydong029.medium.com/?source=post_page---byline--74d8692f10be--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74d8692f10be--------------------------------) ·阅读时长 9 分钟·2024 年 7 月 1 日

--

SQL 可能是每个数据分析师和数据科学家都应该掌握的最基本的技术技能。它通常是面试过程的一部分，我们在工作中花费大量时间编写 SQL 来收集数据。没有它，就没有分析或机器学习模型。然而，编写一个功能性查询与编写一个好的查询是不同的。

在我最近的工作中，我们的数据科学家每天在 Airflow 上安排了超过 1,000 个查询。我注意到我们的 Snowflake 积分在短短一年内增加了 50%。当我自愿检查前 10 个积分使用查询时，很快意识到存在许多优化机会，有些非常简单。本文重点介绍了六个简单而有效的技巧，帮助我每天将 SQL 查询运行时间减少了 50 小时。其中一些非常简单易行，但请记住，SQL 优化中的 20%努力会带来 80%的性能改进。

注意：

1.  本文是为那些已经熟悉基本 SQL 语法的人编写的。如果你是一位每天写 SQL 但想改进查询（并让你的数据工程师朋友高兴）的数据分析师或数据科学家，那么这篇文章适合你！

1.  本文主要基于我在 Snowflake SQL 方面的经验。我专注于…
