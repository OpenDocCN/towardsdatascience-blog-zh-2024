# 《检索增强生成（RAG）实用指南》

> 原文：[https://towardsdatascience.com/a-practitioners-guide-to-retrieval-augmented-generation-rag-36fd38786a84?source=collection_archive---------2-----------------------#2024-03-26](https://towardsdatascience.com/a-practitioners-guide-to-retrieval-augmented-generation-rag-36fd38786a84?source=collection_archive---------2-----------------------#2024-03-26)

## 基本技术如何用于构建强大的LLM应用程序…

[](https://wolfecameron.medium.com/?source=post_page---byline--36fd38786a84--------------------------------)[![Cameron R. Wolfe, Ph.D.](../Images/52bb88d7cf1105501be2fae5ccbe7a03.png)](https://wolfecameron.medium.com/?source=post_page---byline--36fd38786a84--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--36fd38786a84--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--36fd38786a84--------------------------------) [Cameron R. Wolfe博士](https://wolfecameron.medium.com/?source=post_page---byline--36fd38786a84--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--36fd38786a84--------------------------------) ·27分钟阅读·2024年3月26日

--

![](../Images/e09707c11f8f2b650d60672bcd689228.png)

（照片由 [Matthew Dockery](https://unsplash.com/@matt_dockery?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/closeup-photo-of-person-wiping-white-racing-card-s99-JP8P3Hg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

最近对生成式AI的兴趣激增，导致了许多可以用于解决各种任务的AI助手的出现，这些任务包括从 [购物产品](https://www.aboutamazon.com/news/retail/amazon-rufus) 到 [搜索相关信息](https://www.perplexity.ai/)。所有这些有趣的应用都依赖于现代大语言模型（LLM）的进展，这些模型通过海量文本信息的训练，积累了庞大的知识库。然而，LLM在检索和处理它们所拥有的知识方面有着臭名昭著的不足，这导致了像幻觉（即生成错误信息）、知识截止以及对专业领域理解较差等问题。*我们能否有一种方法来提高LLM访问和利用高质量信息的能力？*

> “如果AI助手要在日常生活中发挥更有用的作用，它们不仅需要能够访问大量信息，更重要的是，能够访问正确的信息。” *——* *来源*

上述问题的答案是明确的“是”。在本概述中，我们将探讨将知识注入大语言模型（LLM）的一种最流行的技术——*检索增强生成（RAG）*…
