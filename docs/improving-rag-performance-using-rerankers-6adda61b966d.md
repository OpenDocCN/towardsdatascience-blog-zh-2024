# 使用重排序器提升 RAG 性能

> 原文：[https://towardsdatascience.com/improving-rag-performance-using-rerankers-6adda61b966d?source=collection_archive---------9-----------------------#2024-06-25](https://towardsdatascience.com/improving-rag-performance-using-rerankers-6adda61b966d?source=collection_archive---------9-----------------------#2024-06-25)

## 关于如何使用重排序器来改善您的 RAG 流水线的教程

[](https://medium.com/@het.trivedi05?source=post_page---byline--6adda61b966d--------------------------------)[![Het Trivedi](../Images/f6f11a66f60cacc6b553c7d1682b2fc6.png)](https://medium.com/@het.trivedi05?source=post_page---byline--6adda61b966d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6adda61b966d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6adda61b966d--------------------------------) [Het Trivedi](https://medium.com/@het.trivedi05?source=post_page---byline--6adda61b966d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6adda61b966d--------------------------------) ·10分钟阅读·2024年6月25日

--

![](../Images/768d657abb899c5596fa24028e1a7b70.png)

作者使用 Stable Diffusion XL 创建

## 介绍

RAG 是工程师在构建 LLM 应用程序时最早尝试的工具之一。它足够易于理解且简单易用。使用向量搜索的主要动机是收集足够相关的上下文，以便 LLM 的输出质量更高。

尽管向量搜索开箱即用时表现相当不错，但仍然存在许多结果不尽如人意的情况。例如，在向量嵌入中，`top k` 的结果可能并不包含所有相关信息。为了解决这个问题，可以将 `top k` 设置为更大的值。然而，这也带来了新的问题。

![](../Images/a86a8eea58ba77bf1ab019498c6156ce.png)

文档数量超过了 LLM 上下文窗口的大小

尽管 LLM 支持更大的上下文窗口，但仍然有信息量的限制。`top k` 值越高，将所有信息适配到上下文中的难度就越大。尽管嵌入是按余弦相似度排序的，但这并不能保证最相关的内容排在最前面。这部分是因为向量搜索通常依赖预先计算的嵌入，而这些嵌入可能并不…
