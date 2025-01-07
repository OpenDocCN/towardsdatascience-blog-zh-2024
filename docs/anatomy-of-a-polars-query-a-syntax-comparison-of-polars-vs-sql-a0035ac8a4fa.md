# Polars 查询的构造：Polars 与 SQL 的语法比较

> 原文：[`towardsdatascience.com/anatomy-of-a-polars-query-a-syntax-comparison-of-polars-vs-sql-a0035ac8a4fa?source=collection_archive---------8-----------------------#2024-03-19`](https://towardsdatascience.com/anatomy-of-a-polars-query-a-syntax-comparison-of-polars-vs-sql-a0035ac8a4fa?source=collection_archive---------8-----------------------#2024-03-19)

## ***从 Pandas 到 Polars 的轻松过渡——通过在 SQL 中短暂停留。***

[](https://medium.com/@benfeifke?source=post_page---byline--a0035ac8a4fa--------------------------------)![Ben Feifke](https://medium.com/@benfeifke?source=post_page---byline--a0035ac8a4fa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a0035ac8a4fa--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0035ac8a4fa--------------------------------) [Ben Feifke](https://medium.com/@benfeifke?source=post_page---byline--a0035ac8a4fa--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0035ac8a4fa--------------------------------) ·7 分钟阅读·2024 年 3 月 19 日

--

秘密已经揭晓！[Polars](https://docs.pola.rs/)是当前最火的技术，每个人都想尝一块 😎

我最近写了一篇文章，《我永久转换从 Pandas 到 Polars 的 3 个原因》，因为，嗯，这是选择 Polars 的最常见使用场景——作为 Pandas 的替代品。然而，尽管这是最常见的用例，从 Pandas 到 Polars 的过渡可能会有些奇怪，因为两者在语法上有很大的不同。

在我之前的博客文章中，我讨论了 Pandas 如何迫使用户以面向对象的编程方法进行数据查询，而 Polars 则允许用户以面向数据的编程方法进行数据查询，类似于 SQL。因此，尽管 Polars 通常作为 Pandas 的替代品，但如果你正在尝试学习 Polars，与其将其与 Pandas 进行比较，不如将其与 SQL 进行比较，这可能是一个更容易的起点。本文的目标正是做到这一点：将 Polars 的语法与 SQL 的语法进行比较，为开始使用 Polars 打下基础。
