# RAG：基于两个索引的混合搜索

> 原文：[https://towardsdatascience.com/rag-hybrid-search-based-on-two-indexes-2b35444fa96d?source=collection_archive---------7-----------------------#2024-03-29](https://towardsdatascience.com/rag-hybrid-search-based-on-two-indexes-2b35444fa96d?source=collection_archive---------7-----------------------#2024-03-29)

## 本文中我将讨论的提议是我已经实现并且目前正在个人项目中进行测试的内容。

[](https://medium.com/@jerome.o.diaz?source=post_page---byline--2b35444fa96d--------------------------------)[![Jérôme DIAZ](../Images/da46d0d03587e88ec5bb56b7a997ec24.png)](https://medium.com/@jerome.o.diaz?source=post_page---byline--2b35444fa96d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2b35444fa96d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2b35444fa96d--------------------------------) [Jérôme DIAZ](https://medium.com/@jerome.o.diaz?source=post_page---byline--2b35444fa96d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b35444fa96d--------------------------------) ·阅读时长 3 分钟·2024年3月29日

--

# 什么是混合搜索？

在 RAG 和向量数据库的背景下，混合搜索指的是通过基于嵌入的语义搜索和对这些文本块内容的全文搜索，来搜索有助于回答问题的文档块。

## 限制

虽然混合搜索应该比纯语义方法给出更好的结果，因为它通过突出包含研究中一些关键词的文本片段，能够提供更相关的文本片段，但仍然有改进的空间。

由于关键词是在用于计算嵌入的相同文本中进行搜索的，那么当文档的*块 A*包含关键词，而同一文档的另一个*块 B*在语义上接近查询时，这个*块 B*也应该帮助回答查询，应该怎么办呢？

我们希望让*块 B*成为检索器返回的文档的一部分，但使用标准的混合搜索可能无法实现这一点。

# 一些现有的解决方案

## 自查询检索器

这种检索器基于元数据过滤。可能有助于过滤向量的关键信息...
