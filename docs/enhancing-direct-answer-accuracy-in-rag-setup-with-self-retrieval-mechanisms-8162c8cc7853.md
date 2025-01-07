# 在RAG设置中通过自我检索机制提升直接答案准确性

> 原文：[https://towardsdatascience.com/enhancing-direct-answer-accuracy-in-rag-setup-with-self-retrieval-mechanisms-8162c8cc7853?source=collection_archive---------8-----------------------#2024-04-30](https://towardsdatascience.com/enhancing-direct-answer-accuracy-in-rag-setup-with-self-retrieval-mechanisms-8162c8cc7853?source=collection_archive---------8-----------------------#2024-04-30)

## 利用大语言模型（LLM）的强大能力显著提升在RAG设置中直接生成答案时检索到的文档上下文的质量。

[](https://agustinus-nalwan.medium.com/?source=post_page---byline--8162c8cc7853--------------------------------)[![Agustinus Nalwan](../Images/7c5ade9ab8bca1d27a317b5c09d1b734.png)](https://agustinus-nalwan.medium.com/?source=post_page---byline--8162c8cc7853--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8162c8cc7853--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8162c8cc7853--------------------------------) [Agustinus Nalwan](https://agustinus-nalwan.medium.com/?source=post_page---byline--8162c8cc7853--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8162c8cc7853--------------------------------) ·26分钟阅读·2024年4月30日

--

![](../Images/53adc4aba888bcd3262f20d540412a12.png)

一个机器人正在搜索信息（图像由Midjourney生成）

# 概述

部署检索增强生成（RAG）工作流到生产环境中引入了一个常见的挑战：在检索阶段召回率显著下降。当提供给直接生成答案的文档上下文缺乏关键信息时，这个问题变得尤为明显，进而影响生成答案的质量。当处理类似性质且由长篇文章构成的文档时，这一挑战尤为突出。在某些情况下，为了回答一个问题，可能需要考虑文章的大部分内容，例如在我们关于汽车的数百万篇编辑文章中进行导航。在这种情况下，结合混合搜索方法的自我检索RAG实现提供了一个有前景的解决方案。

RAG无疑是一个强大的工具，像LangChain或LlamaIndex这样的框架使其集成变得非常简单。这种简便性可能会给人一种RAG是“一刀切”解决方案的印象。然而，在我们努力提升编辑文章搜索工具以实现更丰富语义的搜索时……
