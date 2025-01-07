# 分块艺术：提升RAG架构中AI性能

> 原文：[https://towardsdatascience.com/the-art-of-chunking-boosting-ai-performance-in-rag-architectures-acdbdb8bdc2b?source=collection_archive---------0-----------------------#2024-08-18](https://towardsdatascience.com/the-art-of-chunking-boosting-ai-performance-in-rag-architectures-acdbdb8bdc2b?source=collection_archive---------0-----------------------#2024-08-18)

## 高效的AI驱动检索的关键

[](https://medium.com/@han.heloir?source=post_page---byline--acdbdb8bdc2b--------------------------------)[![Han HELOIR, 博士 ☕️](../Images/53c132b64fda2f1d9ebd6af6d582d24c.png)](https://medium.com/@han.heloir?source=post_page---byline--acdbdb8bdc2b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--acdbdb8bdc2b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--acdbdb8bdc2b--------------------------------) [Han HELOIR, 博士 ☕️](https://medium.com/@han.heloir?source=post_page---byline--acdbdb8bdc2b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--acdbdb8bdc2b--------------------------------) ·13分钟阅读·2024年8月18日

--

[免费链接](/the-art-of-chunking-boosting-ai-performance-in-rag-architectures-acdbdb8bdc2b?sk=ddf15d6997d51dd53bcc09b9e586db6d): 请像这样帮助我[LinkedIn帖子](https://www.linkedin.com/posts/hanheloiryan_the-art-of-chunking-boosting-ai-performance-activity-7230999707009908736-z2Dr?utm_source=share&utm_medium=member_desktop)。

**聪明的人都懒。** 他们会找到解决复杂问题的最有效方法，在最小的努力下实现最大的结果。

在生成性AI应用中，这种高效性是通过分块实现的。就像将一本书分成章节让阅读更容易一样，分块将重要的文本分成较小、易于管理的部分，使其更易于处理和理解。

在探索分块的机制之前，首先需要了解这种技术所运作的更广泛框架：检索增强生成（Retrieval-Augmented Generation，RAG）。

## 什么是RAG？

![](../Images/ae2f4795cb78f2fe11fdff3ee452ab8e.png)

什么是检索增强生成（Retrieval Augmented Generation，RAG）

检索增强生成（RAG）是一种将检索机制与大语言模型（LLM模型）结合的方法。它通过使用检索到的文档来增强AI能力，从而生成更准确和更具上下文丰富性的响应。

## 介绍分块

![](../Images/ac5501253b0318983dbf596c3d908f3a.png)

什么是分块
