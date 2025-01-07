# 如何使用 LLMs 通过 gRAG 查询知识图谱

> 原文：[`towardsdatascience.com/how-to-query-a-knowledge-graph-with-llms-using-grag-38bfac47a322?source=collection_archive---------1-----------------------#2024-11-07`](https://towardsdatascience.com/how-to-query-a-knowledge-graph-with-llms-using-grag-38bfac47a322?source=collection_archive---------1-----------------------#2024-11-07)

## 谷歌、微软、LinkedIn 等许多科技公司都在使用 Graph RAG。为什么？让我们通过从零开始构建一个来理解它。

[](https://medium.com/@cristianleo120?source=post_page---byline--38bfac47a322--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--38bfac47a322--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38bfac47a322--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38bfac47a322--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--38bfac47a322--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38bfac47a322--------------------------------) ·阅读时长 24 分钟·2024 年 11 月 7 日

--

![](img/38d8a63f42d10297ca5009557cefda96.png)

这张图展示了一个知识图谱，图中节点和边缘相互连接，背景为受科技启发的渐变色——此图由作者使用 DALL-E 生成

你可能没有意识到，但你与知识图谱（KGs）的互动频率比你想象的要高得多。它们是许多现代搜索引擎、大型语言模型（LLMs）的检索增强生成（RAG）系统和各种查询工具背后的技术。那么，究竟什么是知识图谱，它们为什么如此关键？让我们深入了解一下。

# 知识图谱简介

知识图谱（KG）是信息的结构化表示，它捕捉了现实世界中的实体及其相互之间的关系。可以想象这样一个网络，每个节点代表一个实体——比如产品、人物或概念——而连接它们的线条则代表它们之间的关系。这个相互连接的网络允许对数据进行丰富的语义理解，其中的重点不仅仅是单个信息片段，而是这些片段如何相互关联。

## 节点

知识图谱的核心是节点（实体）。为了说明这一点，我们可以考虑构建一个……
