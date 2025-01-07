# 掌握SQL优化：从功能性到高效的查询

> 原文：[https://towardsdatascience.com/mastering-sql-optimization-from-functional-to-efficient-queries-74d8692f10be?source=collection_archive---------0-----------------------#2024-07-01](https://towardsdatascience.com/mastering-sql-optimization-from-functional-to-efficient-queries-74d8692f10be?source=collection_archive---------0-----------------------#2024-07-01)

## 帮助我每天减少50小时Snowflake查询时间的六个简单而有效的SQL技巧

[](https://ydong029.medium.com/?source=post_page---byline--74d8692f10be--------------------------------)[![Yu Dong](../Images/55c3c11c76cde72c65eb81a60384a436.png)](https://ydong029.medium.com/?source=post_page---byline--74d8692f10be--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--74d8692f10be--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--74d8692f10be--------------------------------) [于东](https://ydong029.medium.com/?source=post_page---byline--74d8692f10be--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74d8692f10be--------------------------------) ·阅读时长9分钟·2024年7月1日

--

SQL可能是每个数据分析师和数据科学家都应该掌握的最基本的技术技能。它通常是面试过程的一部分，我们在工作中花费大量时间编写SQL来收集数据。没有它，就没有分析或机器学习模型。然而，编写一个功能性查询与编写一个好的查询是不同的。

在我最近的工作中，我们的数据科学家每天在Airflow上安排了超过1,000个查询。我注意到我们的Snowflake积分在短短一年内增加了50%。当我自愿检查前10个积分使用查询时，很快意识到存在许多优化机会，有些非常简单。本文重点介绍了六个简单而有效的技巧，帮助我每天将SQL查询运行时间减少了50小时。其中一些非常简单易行，但请记住，SQL优化中的20%努力会带来80%的性能改进。

注意：

1.  本文是为那些已经熟悉基本SQL语法的人编写的。如果你是一位每天写SQL但想改进查询（并让你的数据工程师朋友高兴）的数据分析师或数据科学家，那么这篇文章适合你！

1.  本文主要基于我在Snowflake SQL方面的经验。我专注于…
