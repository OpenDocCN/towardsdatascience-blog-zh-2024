# 在 Python 中开始使用 SQLite3，创建表格并获取行

> 原文：[`towardsdatascience.com/get-started-with-sqlite3-in-python-creating-tables-fetching-rows-a52bdf41e52a?source=collection_archive---------9-----------------------#2024-06-18`](https://towardsdatascience.com/get-started-with-sqlite3-in-python-creating-tables-fetching-rows-a52bdf41e52a?source=collection_archive---------9-----------------------#2024-06-18)

## 学习使用 SQLite — 世界上使用最广泛的数据库管理系统

[](https://medium.com/@ebbeberge?source=post_page---byline--a52bdf41e52a--------------------------------)![Eirik Berge, 博士](https://medium.com/@ebbeberge?source=post_page---byline--a52bdf41e52a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a52bdf41e52a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a52bdf41e52a--------------------------------) [Eirik Berge, 博士](https://medium.com/@ebbeberge?source=post_page---byline--a52bdf41e52a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a52bdf41e52a--------------------------------) ·10 分钟阅读·2024 年 6 月 18 日

--

![](img/0ca4ed3faec8f1e0e5af24f16eaa7cb4.png)

照片由 [Sunder Muthukumaran](https://unsplash.com/@sunder_2k25?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 概述

1.  介绍 — 什么是 SQLite 和 SQLite3？

1.  创建我们的第一个 SQLite 数据库

1.  连接器和游标

1.  创建表格

1.  向数据库插入行

1.  从数据库中获取行

1.  总结

# 介绍 — 什么是 SQLite 和 SQLite3？

大多数现代 IT 专业人员的核心技能之一是 **结构化查询语言 (SQL)**。这是一种声明式语言，用于与关系型数据库进行交互。数据工程师和分析师通常使用 SQL 来运行数据管道并调查数据中的有用关系。

直接使用像 PostgreSQL 或 MySQL 这样的常见**数据库管理系统 (DBMS)**可能会让没有 SQL 经验的人感到有些害怕。幸运的是，**SQLite** 是学习 SQL 基础的一个极好的选择。它的设置非常简单，且易于管理，因为它没有独立的服务器进程。因此，尽管数据工程师和数据分析师通常会使用与 SQLite 不同的数据库管理系统，但它仍然是一个非常好的入门选择…
