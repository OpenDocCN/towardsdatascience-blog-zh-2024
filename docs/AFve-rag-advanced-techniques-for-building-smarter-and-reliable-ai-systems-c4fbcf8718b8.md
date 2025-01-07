# 超越天真的RAG：构建更智能和可靠的AI系统的高级技术

> 原文：[https://towardsdatascience.com/beyond-na%C3%AFve-rag-advanced-techniques-for-building-smarter-and-reliable-ai-systems-c4fbcf8718b8?source=collection_archive---------0-----------------------#2024-10-16](https://towardsdatascience.com/beyond-na%C3%AFve-rag-advanced-techniques-for-building-smarter-and-reliable-ai-systems-c4fbcf8718b8?source=collection_archive---------0-----------------------#2024-10-16)

## 深入研究高级索引、预检索、检索和后检索技术，以增强RAG性能

[](https://medium.com/@abhinavkimothi?source=post_page---byline--c4fbcf8718b8--------------------------------)[![Abhinav Kimothi](../Images/ed5548c99e1910e7dc35bfcac26cb314.png)](https://medium.com/@abhinavkimothi?source=post_page---byline--c4fbcf8718b8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c4fbcf8718b8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c4fbcf8718b8--------------------------------) [Abhinav Kimothi](https://medium.com/@abhinavkimothi?source=post_page---byline--c4fbcf8718b8--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4fbcf8718b8--------------------------------)·阅读27分钟·2024年10月16日

--

![](../Images/adb16eb0ee3a30cf9090b8342fd86bbc.png)

超越天真的RAG：高级RAG技术（来源：作者提供的图片）

你是否曾经向生成式AI应用程序（如ChatGPT）提出问题，发现答案不完整、过时或完全错误？如果有一种方法可以修复这个问题并使AI更准确呢？有！它被称为检索增强生成（Retrieval Augmented Generation，简称RAG）。在Lewis等人在他们的开创性论文[*用于知识密集型NLP任务的检索增强生成*](https://arxiv.org/abs/2005.11401)中引入的一种新概念，RAG迅速成为增强大型语言模型（LLMs）输出的可靠性和可信度的基石。已经证明，LLMs在其参数中存储事实知识，也称为**参数记忆**，而这些知识根植于LLM训练的数据中。RAG通过让LLMs访问外部信息存储库或**知识库**来增强LLMs的知识。这个知识库也被称为**非参数记忆**（因为它不存储在模型参数中）。在2024年，[RAG是生成式AI应用程序中最广泛使用的技术之一](https://intelliarts.com/blog/retrieval-augmented-generation-language-models/#:~:text=As%20evidence%2C%20Databricks%20reports%20that,RAG%20seems%20more%20than%20beneficial.)。

> 60%的LLM应用程序利用某种形式的RAG
