# 问答系统：主要架构概览

> 原文：[https://towardsdatascience.com/question-answering-systems-overview-of-main-architectures-46b94d58bae6?source=collection_archive---------5-----------------------#2024-02-28](https://towardsdatascience.com/question-answering-systems-overview-of-main-architectures-46b94d58bae6?source=collection_archive---------5-----------------------#2024-02-28)

## 探索构建可扩展信息检索系统的设计方法

[](https://medium.com/@slavahead?source=post_page---byline--46b94d58bae6--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--46b94d58bae6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--46b94d58bae6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--46b94d58bae6--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--46b94d58bae6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--46b94d58bae6--------------------------------) ·阅读时间6分钟·2024年2月28日

--

![](../Images/6128b698c58807ef1c94da783d09640a.png)

# 介绍

**问答**应用近年来迅速兴起，几乎无处不在：现代搜索引擎、聊天机器人或能够从大量主题数据中提取相关信息的应用程序。

正如其名称所示，问答应用的目标是从文本段落中提取出最适合的问题答案。最初的一些方法包括通过关键词或正则表达式进行简单搜索。显然，这些方法并不理想：问题或文本中可能包含错别字。此外，正则表达式无法识别与查询中给定单词高度相关的同义词。因此，这些方法逐渐被更加稳健的新方法所取代，尤其是在**变换器**和**向量数据库**的时代。

本文涵盖了构建现代化、可扩展的问答应用程序的三种主要设计方法。

![](../Images/16ae9107d2fef1f12a0522e90c495a2c.png)

问答系统架构类型

# 提取式问答
