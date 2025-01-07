# 解析：为更好的RAG进行分块

> 原文：[https://towardsdatascience.com/breaking-it-down-chunking-techniques-for-better-rag-3fd288bf25a0?source=collection_archive---------3-----------------------#2024-09-23](https://towardsdatascience.com/breaking-it-down-chunking-techniques-for-better-rag-3fd288bf25a0?source=collection_archive---------3-----------------------#2024-09-23)

## 掌握分块技术以提高RAG系统中的信息检索效率

[](https://medium.com/@abhinavkimothi?source=post_page---byline--3fd288bf25a0--------------------------------)[![Abhinav Kimothi](../Images/ed5548c99e1910e7dc35bfcac26cb314.png)](https://medium.com/@abhinavkimothi?source=post_page---byline--3fd288bf25a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3fd288bf25a0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3fd288bf25a0--------------------------------) [Abhinav Kimothi](https://medium.com/@abhinavkimothi?source=post_page---byline--3fd288bf25a0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3fd288bf25a0--------------------------------) ·阅读时长：16分钟·2024年9月23日

--

![](../Images/abcbc7730840cce9e65a7c001518abe9.png)

分块是RAG系统中的关键组成部分（来源：作者使用FLUX生成的AI图像）

# 前言：RAG和知识库的需求

在短短的时间里，**大语言模型（LLMs）**已经在现代语言处理任务中找到了广泛的应用，甚至为自主AI代理铺平了道路。你很可能听说过（如果没有亲自使用过）ChatGPT。ChatGPT由一种叫做大语言模型的生成式AI技术驱动。

**检索增强生成（RAG）**已成为应用生成AI领域中最流行的技术之一。尽管大语言模型展示了前所未有的文本生成能力，但它们的回答并不总是准确的。经过更仔细的观察，你可能会发现，LLM的回答常常存在次优信息和固有的记忆限制。[RAG通过为这些模型提供外部信息来解决LLM的这些局限性。](https://arxiv.org/abs/2005.11401) 这样，LLM的回答变得更加可靠和值得信赖。RAG的基本概念 *如下示例所示*。在这里，我们向ChatGPT提供外部信息（手动）以使其准确回答。
