# 使用评估优化 RAG 流水线：从分块和嵌入到 LLM

> 原文：[`towardsdatascience.com/using-evaluations-to-optimize-a-rag-pipeline-from-chunkings-and-embeddings-to-llms-40e5ed6033b8?source=collection_archive---------5-----------------------#2024-07-12`](https://towardsdatascience.com/using-evaluations-to-optimize-a-rag-pipeline-from-chunkings-and-embeddings-to-llms-40e5ed6033b8?source=collection_archive---------5-----------------------#2024-07-12)

## 使用 Milvus 向量数据库的最佳实践 RAG，第二部分

[](https://medium.com/@christybergman?source=post_page---byline--40e5ed6033b8--------------------------------)![Christy Bergman](https://medium.com/@christybergman?source=post_page---byline--40e5ed6033b8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40e5ed6033b8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40e5ed6033b8--------------------------------) [Christy Bergman](https://medium.com/@christybergman?source=post_page---byline--40e5ed6033b8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40e5ed6033b8--------------------------------) ·阅读时间 7 分钟·2024 年 7 月 12 日

--

![](img/b8ef251d4f0bd38f28fc612832fb483c.png)

由作者使用 [`www.bing.com/images/create`](https://www.bing.com/images/create) 创建的图像。内容凭证：**由 AI 生成** ∙ 2024 年 7 月 9 日上午 10:04。

检索增强生成（RAG）是一种在 AI 驱动的聊天机器人中使用自身数据的有用技术。在这篇博客文章中，我将介绍三种关键策略，帮助你从 RAG 中获得最大收益，并评估每种策略，找出最佳组合。

对于那些只想了解 TL;DR 结论的读者：RAG 准确度的最大提升来自于探索不同的分块策略。

+   **通过更改分块策略提高 84%** 📦

+   **通过更改嵌入模型提高 20%** 🤖

+   **通过更改 LLM 模型提高 6%** 🧪

让我们深入探讨每种策略，使用 RAG 组件评估找到适用于实际应用的最佳表现者！🚀📚

我将使用 [Milvus（由中国初创公司 Zilliz 开发的向量数据库）文档](https://milvus.io/docs) 公共网页作为文档数据，并使用 [Ragas](https://github.com/explodinggradients/ragas) 作为评估方法。请参阅我之前的 [博客，了解如何使用 RAGAS](https://medium.com/towards-data-science/rag-evaluation-using-ragas-4645a4c6c477)。本博客的其余部分组织如下：

1.  文本分块策略

1.  嵌入模型

1.  LLM（生成）模型
