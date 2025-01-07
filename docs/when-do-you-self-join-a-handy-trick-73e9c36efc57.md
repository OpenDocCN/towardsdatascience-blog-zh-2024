# 你什么时候使用自连接？一个实用的技巧

> 原文：[`towardsdatascience.com/when-do-you-self-join-a-handy-trick-73e9c36efc57?source=collection_archive---------5-----------------------#2024-03-17`](https://towardsdatascience.com/when-do-you-self-join-a-handy-trick-73e9c36efc57?source=collection_archive---------5-----------------------#2024-03-17)

## SQL 中级

## 中级 SQL 用于 ETL 开发到数据工程师的过渡

[](https://medium.com/@withsaikat?source=post_page---byline--73e9c36efc57--------------------------------)![Saikat Dutta](https://medium.com/@withsaikat?source=post_page---byline--73e9c36efc57--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--73e9c36efc57--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--73e9c36efc57--------------------------------) [Saikat Dutta](https://medium.com/@withsaikat?source=post_page---byline--73e9c36efc57--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--73e9c36efc57--------------------------------) ·7 分钟阅读·2024 年 3 月 17 日

--

![](img/46d7fc66733d8563e73f2c0afc2cf7e6.png)

由[Campaign Creators](https://unsplash.com/@campaign_creators?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在 SQL 中并没有所谓的自连接。听我说完。

数据分析师和数据工程师经常需要在数据中发现不明显的模式。然而，通过使用常见的 SQL 实践，如自连接，能够生成见解并识别出这些模式。

很多初学者常常很难理解自连接。他们将 SQL 中的自连接误认为是像内连接或左连接那样的另一个命令。但自连接并不是 SQL 中的一个关键词，它也不是 SQL 中的一个命令。

自连接就像是两个表之间的普通连接（内连接/左连接/右连接/外连接）。然而，在自连接中，这两个表是相同的，但通过它们的别名表现为不同的表。

自连接在数据工程中常常被认为是一种不好的实践。人们认为使用它有风险。但在某些场景下，使用自连接是实际且解决问题的最佳方式。

让我们看几个例子：

## **层次化数据：**

自连接在处理层次化数据时非常有用。在组织结构图中，我们可以基于经理-员工关系将一个表与自身连接，以找到员工的报告...
