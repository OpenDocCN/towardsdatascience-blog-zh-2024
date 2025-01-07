# Polars查询的构造：Polars与SQL的语法比较

> 原文：[https://towardsdatascience.com/anatomy-of-a-polars-query-a-syntax-comparison-of-polars-vs-sql-a0035ac8a4fa?source=collection_archive---------8-----------------------#2024-03-19](https://towardsdatascience.com/anatomy-of-a-polars-query-a-syntax-comparison-of-polars-vs-sql-a0035ac8a4fa?source=collection_archive---------8-----------------------#2024-03-19)

## ***从Pandas到Polars的轻松过渡——通过在SQL中短暂停留。***

[](https://medium.com/@benfeifke?source=post_page---byline--a0035ac8a4fa--------------------------------)[![Ben Feifke](../Images/dd4eb09e404dbd3a478ea1049cf17c90.png)](https://medium.com/@benfeifke?source=post_page---byline--a0035ac8a4fa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a0035ac8a4fa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a0035ac8a4fa--------------------------------) [Ben Feifke](https://medium.com/@benfeifke?source=post_page---byline--a0035ac8a4fa--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0035ac8a4fa--------------------------------) ·7分钟阅读·2024年3月19日

--

秘密已经揭晓！[Polars](https://docs.pola.rs/)是当前最火的技术，每个人都想尝一块 😎

我最近写了一篇文章，[《我永久转换从Pandas到Polars的3个原因》](/the-3-reasons-why-i-have-permanently-switched-from-pandas-to-polars-b41d013a787b)，因为，嗯，这是选择Polars的最常见使用场景——作为Pandas的替代品。然而，尽管这是最常见的用例，从Pandas到Polars的过渡可能会有些奇怪，因为两者在语法上有很大的不同。

在我之前的博客文章中，我讨论了Pandas如何迫使用户以面向对象的编程方法进行数据查询，而Polars则允许用户以面向数据的编程方法进行数据查询，类似于SQL。因此，尽管Polars通常作为Pandas的替代品，但如果你正在尝试学习Polars，与其将其与Pandas进行比较，不如将其与SQL进行比较，这可能是一个更容易的起点。本文的目标正是做到这一点：将Polars的语法与SQL的语法进行比较，为开始使用Polars打下基础。
