# AI驱动的（向量）搜索基础

> 原文：[https://towardsdatascience.com/the-basics-of-ai-powered-vector-search-984472a0581f?source=collection_archive---------4-----------------------#2024-03-18](https://towardsdatascience.com/the-basics-of-ai-powered-vector-search-984472a0581f?source=collection_archive---------4-----------------------#2024-03-18)

## 现代AI的兴起如何彻底改变了搜索应用…

[](https://wolfecameron.medium.com/?source=post_page---byline--984472a0581f--------------------------------)[![Cameron R. Wolfe, Ph.D.](../Images/52bb88d7cf1105501be2fae5ccbe7a03.png)](https://wolfecameron.medium.com/?source=post_page---byline--984472a0581f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--984472a0581f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--984472a0581f--------------------------------) [Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--984472a0581f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--984472a0581f--------------------------------) ·32分钟阅读·2024年3月18日

--

![](../Images/bcb51159254e4ea2a3c9b105343e669c.png)

(照片由[Tamanna Rumee](https://unsplash.com/@tamanna_rumee?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来自[Unsplash](https://unsplash.com/photos/yellow-and-blue-color-paper-FtJEat_S7Q4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash))

最近，生成式AI的兴起和大型语言模型（LLMs）的出现让许多人开始思考搜索引擎的发展。*基于对话的LLM是否会取代传统搜索引擎，或者这些模型易于产生幻觉的特性是否会使它们成为不可信的信息源？*目前，这些问题的答案尚不明晰，但AI中心的搜索系统如[you.com](https://you.com/)和[perplexity.ai](https://www.perplexity.ai/)的快速普及表明，越来越多的人希望将现代语言模型的进展应用于搜索引擎。具有讽刺意味的是，*我们多年来一直在搜索引擎中大量使用语言模型*！BERT的提出[1]使得我们在评估语义文本相似性方面取得了飞跃式的进步，导致这些语言模型被多种流行的搜索引擎采纳（[包括Google](https://blog.google/products/search/search-language-understanding-bert/)！）。在本概述中，我们将分析这些AI驱动的搜索系统的组成部分。

# 搜索引擎的基本组成部分

![](../Images/7d902347e24bd967eca2669b686bdba3.png)

搜索引擎中的检索与排名（由作者创建）
