# BigQuery 方法用于重新创建 Pandas 的顶级 EDA 函数

> 原文：[https://towardsdatascience.com/bigquery-methods-for-re-creating-pandas-top-eda-functions-487d3f1abe07?source=collection_archive---------14-----------------------#2024-02-05](https://towardsdatascience.com/bigquery-methods-for-re-creating-pandas-top-eda-functions-487d3f1abe07?source=collection_archive---------14-----------------------#2024-02-05)

## 在本指南中，我们将探讨如何在 BigQuery 中重新创建用于 EDA 的关键 Pandas 函数，如 describe 和 corr。

[](https://medium.com/@thomas.ellyatt?source=post_page---byline--487d3f1abe07--------------------------------)[![Tom Ellyatt](../Images/8756acdd11fef8db9a868820251e7575.png)](https://medium.com/@thomas.ellyatt?source=post_page---byline--487d3f1abe07--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--487d3f1abe07--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--487d3f1abe07--------------------------------) [Tom Ellyatt](https://medium.com/@thomas.ellyatt?source=post_page---byline--487d3f1abe07--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--487d3f1abe07--------------------------------) ·阅读时间：21分钟·2024年2月5日

--

![](../Images/2046af8f74e4abf2694bbd8d2c9bcf3e.png)

图像由 DALL-E 创建

从 BigQuery/SQL 迁移到 Python 可能会让人眼前一亮，尤其是在数据分析的背景下。我常常发现自己在 BigQuery SQL 中编写大量查询来操作和分析数据。它是一个强大的语言，但有时会变得很沉重。

现在，当我转到 Python 时，我对某些任务的简化程度感到惊讶。像 pandas 这样的 Python 库允许你进行数据操作和分析，而这些在 SQL 中会显得繁琐。

我发现一些 Pandas 函数如 **DESCRIBE**、**CORR** 和 **ISNULL().SUM()** 非常有用，我希望它们也能出现在 BigQuery 中。这让我开始探索 Pandas 中的其他有趣的 EDA 函数，并激发了我写这篇文章的灵感。在这里，我将分享我在 BigQuery 中找到的与 Pandas 最佳 EDA 函数相匹配的方法和代码。

让我们开始吧！

## 在本文中，我们将看看这13个函数：

1.  [头部 / 尾部](#c763)

1.  [列](#9301)

1.  [数据类型](#83a3)

1.  [Nunique](#6751)
