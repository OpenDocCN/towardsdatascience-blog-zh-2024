# 高级数据建模

> 原文：[https://towardsdatascience.com/advanced-data-modelling-1e496578bc91?source=collection_archive---------0-----------------------#2024-07-21](https://towardsdatascience.com/advanced-data-modelling-1e496578bc91?source=collection_archive---------0-----------------------#2024-07-21)

## 数据模型层、环境、测试和数据质量解析

[](https://mshakhomirov.medium.com/?source=post_page---byline--1e496578bc91--------------------------------)[![💡Mike Shakhomirov](../Images/bc6895c7face3244d488feb97ba0f68e.png)](https://mshakhomirov.medium.com/?source=post_page---byline--1e496578bc91--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1e496578bc91--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1e496578bc91--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--1e496578bc91--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e496578bc91--------------------------------) ·阅读时间 14 分钟·2024年7月21日

--

![](../Images/92d33eb077ec471e016d7ea654ff9788.png)

AI 生成的图像，使用了[Kandinsky](https://github.com/ai-forever/Kandinsky-2)

数据建模是数据工程的重要组成部分。我认为，如果你想成为一名成功的数据从业者，这是必不可少的。构建具有多层的 SQL 转换管道是一个具有挑战性的任务。在这个过程中，保持条理清晰非常重要。在这篇文章中，我尝试总结一些方便数据结构化的技巧，并描述我每天使用的建模技巧。它通常能帮助我设计和开发一个准确、易于导航且用户友好的优秀数据平台或数据仓库。

## 命名规范

使用良好设计的命名规范可以提供非常清晰且无歧义的数据库对象内容的感知和含义。对于表格和列的命名规范始终是一个好习惯。它能很好地展示你的数据仓库有多成熟，并在开发过程中提供极大的帮助。

> 数据库实体名称必须至少是人类可读的。

以此为前提维护数据库或数据仓库可以提升用户体验，使其看起来更加**用户友好**。
