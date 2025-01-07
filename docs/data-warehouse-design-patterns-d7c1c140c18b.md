# 数据仓库设计模式

> 原文：[`towardsdatascience.com/data-warehouse-design-patterns-d7c1c140c18b?source=collection_archive---------2-----------------------#2024-01-29`](https://towardsdatascience.com/data-warehouse-design-patterns-d7c1c140c18b?source=collection_archive---------2-----------------------#2024-01-29)

## 我如何在我的新数据仓库中组织一切

[](https://mshakhomirov.medium.com/?source=post_page---byline--d7c1c140c18b--------------------------------)![💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--d7c1c140c18b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d7c1c140c18b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7c1c140c18b--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--d7c1c140c18b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7c1c140c18b--------------------------------) ·阅读时间 11 分钟·2024 年 1 月 29 日

--

![](img/8a97891cc8b0a162dbc219aa47d103e3.png)

图片来源：[Lidia Nikole](https://unsplash.com/@lidia_nikole?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最近，我为我的新数据项目需要一个数据仓库工具。这个故事讲述了我是如何从零开始构建它，并在其中组织一切的。设计一个数据平台不是一项简单的任务，现代的数据仓库解决方案通常是其架构的核心。它提供了强大的数据治理功能、使用 ANSI SQL 简化的数据查询以及增强的数据建模能力。由于数据源数量庞大且所需转换复杂，组织内部的一切——即数据环境、测试、命名规范、数据库、架构和表格——可能是一项具有挑战性的任务。这个故事可能对希望学习高级数据仓库技术的初学者和中级用户有帮助。对于有经验的数据从业者，我希望能讨论他们对数据仓库设计的看法，以及他们通常如何在其中组织一切。

## 设计数据平台

作为一名数据工程师，我每天都在设计数据管道。这正是现代数据平台的核心内容，它必须具有成本效益、可扩展性，并且在长期内易于维护。为数据密集型应用设计管道总是具有挑战性的，而现代**数据仓库**（DWH）的目标就是简化并增强这一过程……
