# 多模态 RAG：用 AI 处理任何文件类型

> 原文：[https://towardsdatascience.com/multimodal-rag-process-any-file-type-with-ai-e6921342c903?source=collection_archive---------1-----------------------#2024-12-05](https://towardsdatascience.com/multimodal-rag-process-any-file-type-with-ai-e6921342c903?source=collection_archive---------1-----------------------#2024-12-05)

## 适合初学者的指南，包含示例（Python）代码

[](https://shawhin.medium.com/?source=post_page---byline--e6921342c903--------------------------------)[![Shaw Talebi](../Images/1449cc7c08890e2078f9e5d07897e3df.png)](https://shawhin.medium.com/?source=post_page---byline--e6921342c903--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e6921342c903--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e6921342c903--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--e6921342c903--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e6921342c903--------------------------------) ·12分钟阅读·2024年12月5日

--

这是关于[多模态 AI](https://shawhin.medium.com/list/multimodal-ai-fe9521d0e77a)系列文章的第三篇。在之前的文章中，我们分别讨论了[多模态 LLMs](/multimodal-models-llms-that-can-see-and-hear-5c6737c981d3)和[嵌入模型](/multimodal-embeddings-an-introduction-5dc36975966f)。在本文中，我们将结合这些理念，推动多模态 RAG 系统的开发。我将首先回顾关键概念，然后分享实现该系统的示例代码。

![](../Images/646324391e6c96935ae763319c252c85.png)

图片来自 Canva。

类似 GPT、LLaMA 和 Claude 这样的语言模型通过预训练学习了大量的世界知识。这使得它们成为解决定制问题和回答复杂问题的强大工具。

然而，**即使是最先进的语言模型也有它们不知道的知识**。这包括组织内部的专有信息、模型预训练数据收集后发生的事件，以及在互联网上不常见的专业知识。

尽管这种无知限制了模型的开箱即用能力，但有**一种流行的技术可以克服这些**……
