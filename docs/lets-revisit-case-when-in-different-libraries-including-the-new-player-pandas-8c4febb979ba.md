# 让我们重新审视不同库中的 case-when，包括新玩家：Pandas

> 原文：[`towardsdatascience.com/lets-revisit-case-when-in-different-libraries-including-the-new-player-pandas-8c4febb979ba?source=collection_archive---------17-----------------------#2024-06-18`](https://towardsdatascience.com/lets-revisit-case-when-in-different-libraries-including-the-new-player-pandas-8c4febb979ba?source=collection_archive---------17-----------------------#2024-06-18)

## 如何使用不同工具创建条件列。

[](https://sonery.medium.com/?source=post_page---byline--8c4febb979ba--------------------------------)![Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--8c4febb979ba--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8c4febb979ba--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8c4febb979ba--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--8c4febb979ba--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8c4febb979ba--------------------------------) ·6 分钟阅读·2024 年 6 月 18 日

--

![](img/bc06ef7c7230f619353bf30c88265be0.png)

图片由 [JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/three-black-handled-pliers-on-brown-surface-VdOO4_HFTWM?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

无论你是在做数据分析、数据清洗，还是特征工程，基于其他列的值创建新列是一个常见的操作。

我使用过的所有数据清洗和处理工具都有执行此任务的函数（例如 SQL、R 数据表、PySpark）。现在我们有了游戏中的新玩家：Pandas。

顺便提一下，虽然之前可以使用 Pandas 创建条件列，但它并没有专门的 case-when 函数。

在 Pandas 2.2.0 中，引入了 `case_when` 函数，用于根据一个或多个条件创建 Series 对象。

让我们重新审视如何使用常用的数据分析和处理工具完成这个非常有用的操作。

为了保持一致性并更容易看出工具之间的差异，我们将使用一个小型数据集。

## SQL

以下是一个名为“mytable”的小型 SQL 表。

```py
+-------------+----------+---------+
|           a |        b |       c |
+-------------+----------+---------+
|           0 |        5 |       1 |
|           1 |       -1 |…
```
