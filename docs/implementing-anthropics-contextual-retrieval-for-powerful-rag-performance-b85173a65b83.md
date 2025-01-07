# 实现 Anthropic 的上下文检索以提升强大的 RAG 性能

> 原文：[`towardsdatascience.com/implementing-anthropics-contextual-retrieval-for-powerful-rag-performance-b85173a65b83?source=collection_archive---------5-----------------------#2024-10-18`](https://towardsdatascience.com/implementing-anthropics-contextual-retrieval-for-powerful-rag-performance-b85173a65b83?source=collection_archive---------5-----------------------#2024-10-18)

## 本文将向你展示如何实现 Anthropic 提出的上下文检索思想

[](https://oieivind.medium.com/?source=post_page---byline--b85173a65b83--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--b85173a65b83--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b85173a65b83--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b85173a65b83--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--b85173a65b83--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b85173a65b83--------------------------------) ·13 分钟阅读·2024 年 10 月 18 日

--

检索增强生成（RAG）是一种强大的技术，利用大型语言模型（LLMs）和向量数据库来创建更准确的用户查询响应。RAG 使 LLMs 在响应用户查询时能够利用大型知识库，从而提高响应质量。然而，RAG 也有一些缺点。一个缺点是 RAG 在检索上下文以响应用户查询时，使用了向量相似性。向量相似性并非始终一致，例如，在处理独特的用户关键词时可能会遇到困难。此外，由于文本被分割成更小的块，RAG 也面临困难，这限制了 LLM 在响应查询时无法充分利用文档的完整上下文。[Anthropic 的文章](https://www.anthropic.com/news/contextual-retrieval)通过使用 BM25 索引并将上下文添加到块中，尝试解决这两个问题。

![](img/8e1e2d5eb8de85511246ac0f031a1952.png)

通过本文了解如何实现 Anthropic 的上下文检索 RAG。图片来源：ChatGPT。

# 动机

写这篇文章的动机有两个。首先，我想测试机器学习中最新的模型和技术。跟上机器学习领域的最新趋势对于任何机器学习工程师和数据科学家来说都是至关重要的，尤其是在...
