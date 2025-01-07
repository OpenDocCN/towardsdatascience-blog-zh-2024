# SQL和数据建模实战：深入探讨数据湖仓

> 原文：[https://towardsdatascience.com/sql-and-data-modelling-in-action-a-deep-dive-into-data-lakehouses-fcbab9a4b9c2?source=collection_archive---------1-----------------------#2024-10-21](https://towardsdatascience.com/sql-and-data-modelling-in-action-a-deep-dive-into-data-lakehouses-fcbab9a4b9c2?source=collection_archive---------1-----------------------#2024-10-21)

[](https://medium.com/@schuerch_sarah?source=post_page---byline--fcbab9a4b9c2--------------------------------)[![Sarah Lea](../Images/09c9fa20016d1439b70b9fb6670b57a9.png)](https://medium.com/@schuerch_sarah?source=post_page---byline--fcbab9a4b9c2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fcbab9a4b9c2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fcbab9a4b9c2--------------------------------) [Sarah Lea](https://medium.com/@schuerch_sarah?source=post_page---byline--fcbab9a4b9c2--------------------------------)

·发布于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--fcbab9a4b9c2--------------------------------) ·阅读时间10分钟·2024年10月21日

--

任何从事商业智能、数据科学、数据分析或云计算工作的人，都会在某个时候接触到SQL。我们可以用它来提取、操作和分析数据——无论是在关系型数据库中，还是在现代云环境中。为了实施Salesforce数据云，我不得不复习一下我的SQL和数据建模知识。

> 有趣的事实：你知道最常用的SQL命令是什么吗？

阅读完整篇文章，你将会找到答案 ;)

> ***内容***[*1 —* 什么是数据湖、数据仓库和数据湖仓？](#8499)
> 
> [2 — 它与商业智能工具和云存储有什么区别？](#6eb0)
> 
> [3 — 为什么SQL和数据建模对于数据湖仓或特定工具（如Salesforce数据云）很重要](#cc43)
> 
> [4 — 云应用程序中的SQL和数据建模基础](#886d)
> 
> [5 — Salesforce数据云与其他云工具的区别](#d664)
> 
> [6 — 数据湖仓对数据科学家的关键使用案例](#a787)
> 
> [7\. 最后的思考](#4c55)

# 1 — 什么是数据湖、数据仓库和数据湖仓？

当我们谈论数据平台或数据架构时，需要理解数据湖、数据仓库和数据湖仓的概念。由于我们生活在一个数据日益增长的世界中——几年前总是有人说“数据是新的黄金”——因此，我们也需要能够存储、处理和利用大量数据的系统……
