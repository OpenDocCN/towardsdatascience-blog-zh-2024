# 如何为你的 RAG 找到最佳的多语言嵌入模型

> 原文：[`towardsdatascience.com/how-to-find-the-best-multilingual-embedding-model-for-your-rag-40325c308ebb?source=collection_archive---------2-----------------------#2024-01-27`](https://towardsdatascience.com/how-to-find-the-best-multilingual-embedding-model-for-your-rag-40325c308ebb?source=collection_archive---------2-----------------------#2024-01-27)

## 优化嵌入空间以提升 RAG

[](https://medium.com/@brezeanu.iulia?source=post_page---byline--40325c308ebb--------------------------------)![Iulia Brezeanu](https://medium.com/@brezeanu.iulia?source=post_page---byline--40325c308ebb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40325c308ebb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40325c308ebb--------------------------------) [Iulia Brezeanu](https://medium.com/@brezeanu.iulia?source=post_page---byline--40325c308ebb--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40325c308ebb--------------------------------) ·8 分钟阅读·2024 年 1 月 27 日

--

![](img/c55302a3dbb63f604fc377ef5c995fee.png)

图片来自作者，AI 生成。

嵌入是捕捉单词或句子语义含义的向量表示。除了拥有优质的数据，选择一个好的嵌入模型是优化你的 RAG 应用程序中最重要且最被低估的步骤。多语言模型尤其具有挑战性，因为大多数模型都是在英语数据上预训练的。正确的嵌入会带来巨大的差异——不要仅仅选择你看到的第一个模型！

语义空间决定了单词和概念之间的关系。准确的语义空间能够提高检索性能。不准确的嵌入会导致无关的片段或丢失的信息。更好的模型直接提升你 RAG 系统的能力。

在本文中，我们将从 PDF 文档中创建一个问答数据集，以便为我们的任务和语言找到最佳模型。在 RAG 过程中，如果能够检索到期望的答案，这意味着嵌入模型将问题和答案在语义空间中定位得足够接近。

虽然我们专注于法语和意大利语，但这个过程可以适应任何语言，因为最佳的嵌入可能会有所不同。

## 嵌入模型
