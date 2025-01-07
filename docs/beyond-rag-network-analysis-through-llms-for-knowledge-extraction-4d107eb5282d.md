# 超越 RAG：通过 LLM 进行网络分析以提取知识

> 原文：[`towardsdatascience.com/beyond-rag-network-analysis-through-llms-for-knowledge-extraction-4d107eb5282d?source=collection_archive---------6-----------------------#2024-02-24`](https://towardsdatascience.com/beyond-rag-network-analysis-through-llms-for-knowledge-extraction-4d107eb5282d?source=collection_archive---------6-----------------------#2024-02-24)

## *使用 Streamlit、Upstash 和 OpenAI 的端到端数据科学项目，通过网络分析构建更好的知识导航和理解*

[](https://medium.com/@theDrewDag?source=post_page---byline--4d107eb5282d--------------------------------)![Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--4d107eb5282d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4d107eb5282d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4d107eb5282d--------------------------------) [Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--4d107eb5282d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4d107eb5282d--------------------------------) ·阅读时间 26 分钟·2024 年 2 月 24 日

--

![](img/358d064472f4b857338b33cc49a3a91f.png)

[USGS](https://unsplash.com/@usgs?utm_source=medium&utm_medium=referral)提供的照片，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文将引导您通过一个使用多个前沿 AI 工具的端到端数据科学项目。这个工具被称为*Mind Mapper*，因为它允许你通过将信息注入知识库并以智能方式检索它来创建概念图。

动机是超越“简单”的 RAG 框架，在该框架中，用户查询向量数据库，然后将其响应输入 LLM（如 GPT-4）以获得更丰富的答案。

Mind Mapper 利用 RAG 来创建中间结果表示，这对于执行**某种知识智能**非常有用，这也使我们能够更好地理解 RAG 在长篇且无结构文档中的输出结果。

简单来说，我想将 RAG 作为构建多样化响应的基础步骤，不仅仅是文本回应。思维导图就是其中一种响应。

以下是该工具的一些功能：

+   **管理几乎所有形式的文本**：复制粘贴的文本、文本以及来自音频源的文本（视频是...
