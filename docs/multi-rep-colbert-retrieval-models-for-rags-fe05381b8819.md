# 没有检索模型构建 RAG 是一个可怕的错误

> 原文：[`towardsdatascience.com/multi-rep-colbert-retrieval-models-for-rags-fe05381b8819?source=collection_archive---------0-----------------------#2024-09-17`](https://towardsdatascience.com/multi-rep-colbert-retrieval-models-for-rags-fe05381b8819?source=collection_archive---------0-----------------------#2024-09-17)

## 这是我最喜欢的两种技术——一种更快，另一种更精确。

[](https://thuwarakesh.medium.com/?source=post_page---byline--fe05381b8819--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--fe05381b8819--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe05381b8819--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe05381b8819--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--fe05381b8819--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe05381b8819--------------------------------) ·阅读时间：9 分钟·2024 年 9 月 17 日

--

![](img/900e715237e25452c57066ba1c08ff6f.png)

图片来自[Alexander Grey](https://www.pexels.com/photo/person-holding-multicolored-container-1209870/)

我构建 RAG 应用；这很有趣！

但我构建的应用在生产环境中表现不佳。它们是有前景的原型，但从未上线！

问题几乎总是出在检索上。来吧，这是 RAG 的核心。如果没有这个，我们还能构建什么呢？

直到我**为更快或更好的检索对文档进行索引**。

索引帮助我们设计能够更快检索数据的解决方案。它显著减少了延迟，提升了整体应用体验。几乎每个我们构建的应用都使用了索引。它与 LLMs 或 RAGs 无关。

几乎所有数据库都支持索引。例如，[Postgres](https://www.freecodecamp.org/news/postgresql-indexing-strategies/)可以进行*B-Tree, GiST, SP-GiST, BRIN, GIN 和 Hash*类型的索引。这是一个足够长的列表，可能会成为未来一篇独立文章的主题。

在这篇文章中，我将讨论我经常使用的流行索引策略，以便更好地进行文档检索。然而，这些技术是特定于 RAG 应用的。你很快就会明白为什么。

我常用的两种索引技术是**多重表示和 ColBERT**。这并不是我们拥有的唯一方法。而且…
