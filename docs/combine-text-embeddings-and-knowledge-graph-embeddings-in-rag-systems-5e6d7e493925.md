# 在RAG系统中结合文本嵌入和知识（图）嵌入

> 原文：[https://towardsdatascience.com/combine-text-embeddings-and-knowledge-graph-embeddings-in-rag-systems-5e6d7e493925?source=collection_archive---------0-----------------------#2024-04-29](https://towardsdatascience.com/combine-text-embeddings-and-knowledge-graph-embeddings-in-rag-systems-5e6d7e493925?source=collection_archive---------0-----------------------#2024-04-29)

[](https://sunila-gollapudi.medium.com/?source=post_page---byline--5e6d7e493925--------------------------------)[![Sunila Gollapudi](../Images/c29dbcf1766e3e0a3ca576d301454417.png)](https://sunila-gollapudi.medium.com/?source=post_page---byline--5e6d7e493925--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5e6d7e493925--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5e6d7e493925--------------------------------) [Sunila Gollapudi](https://sunila-gollapudi.medium.com/?source=post_page---byline--5e6d7e493925--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5e6d7e493925--------------------------------) ·阅读时间 15 分钟·2024年4月29日

--

在我之前的文章中，我写了如何将[知识图谱与RAG结合使用](https://medium.com/techspresso/using-knowledge-graphs-to-enhance-retrieval-augmented-generation-rag-systems-14197efc1bab)以及如何利用[图形技术进行自适应分词](https://medium.com/techspresso/advanced-tokenization-in-llms-usage-of-advanced-graph-techniques-e684a53c5e59)来构建更具上下文感知的LLM。在本文中，我很高兴展示我在将文本嵌入和知识（图）嵌入结合使用以及RAG性能的观察中的实验。我将首先解释文本和知识嵌入的概念，分别使用简单的开放框架，然后，我们将看到如何在RAG应用中使用这两者。这篇文章相当长，我故意不想将其分成多个部分，以便大家可以顺畅阅读，并按照我下面分享的顺序进行尝试。

![](../Images/036b14c9b55f0c87dff43763b45730f7.png)

作者创建的领域相关语料库

我将深入探讨并分四部分讲解我的工作，具体如下所列。

+   **第1部分：** 什么是文本嵌入（TE）？它们是如何存储和在RAG实现中使用的？

+   **第2部分：** 什么是知识（图）嵌入（KGE）以及它们是如何存储的？

+   **第3部分：** 知识（图）嵌入与文本嵌入有什么不同，并分析它们在RAG使用中的互补性

+   **结论：** 在RAG中结合文本和知识嵌入的好处及整体总结

# 第1部分：文本嵌入与RAG...
