# 提升检索增强生成（RAG）系统的 9 种有效技术

> 原文：[`towardsdatascience.com/9-effective-techniques-to-boost-retrieval-augmented-generation-rag-systems-210ace375049?source=collection_archive---------1-----------------------#2024-01-02`](https://towardsdatascience.com/9-effective-techniques-to-boost-retrieval-augmented-generation-rag-systems-210ace375049?source=collection_archive---------1-----------------------#2024-01-02)

## ReRank、混合搜索、查询扩展、利用元数据等……

[](https://ahmedbesbes.medium.com/?source=post_page---byline--210ace375049--------------------------------)![Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--210ace375049--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--210ace375049--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--210ace375049--------------------------------) [Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--210ace375049--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--210ace375049--------------------------------) ·阅读时间 7 分钟·2024 年 1 月 2 日

--

![](img/a9e5180173374071165cafe6e554901a.png)

图像由 Bing 生成

2023 年是自然语言处理（NLP）历史上最富有成效的一年。这一时期，ChatGPT 以及众多其他大型语言模型的出现，既有开源的也有专有的。

与此同时，微调大型语言模型（LLM）变得更加容易，云服务提供商之间在生成式人工智能（GenAI）产品上的竞争也显著加剧。

有趣的是，个性化且功能完备的 RAG 需求在各行业中激增，每个客户都急于拥有自己量身定制的解决方案。

> ***说到这一点，关于创建功能完善的 RAG，今天我们将讨论一篇*** [***论文***](https://arxiv.org/pdf/2312.10997.pdf) ***，该论文回顾了当前构建这些系统的最新技术。***

不再浪费时间，让我们一起来看看 🔍

> 如果你对机器学习内容感兴趣，想要了解来自业界的详细教程和实用技巧，请关注我的[新闻通讯](https://thetechbuffet.substack.com/)。它叫做《The Tech Buffet》。
