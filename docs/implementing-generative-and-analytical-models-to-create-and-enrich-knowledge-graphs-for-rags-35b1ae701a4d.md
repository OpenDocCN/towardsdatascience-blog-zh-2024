# 实现生成性和分析性模型，创建和丰富 RAG 的知识图谱

> 原文：[`towardsdatascience.com/implementing-generative-and-analytical-models-to-create-and-enrich-knowledge-graphs-for-rags-35b1ae701a4d?source=collection_archive---------4-----------------------#2024-05-29`](https://towardsdatascience.com/implementing-generative-and-analytical-models-to-create-and-enrich-knowledge-graphs-for-rags-35b1ae701a4d?source=collection_archive---------4-----------------------#2024-05-29)

## 评估生成模型和分析模型，以构建知识图谱，并促进这些增强的、以领域为中心的知识图谱，支持高效的 RAG 系统。

[](https://sunila-gollapudi.medium.com/?source=post_page---byline--35b1ae701a4d--------------------------------)![Sunila Gollapudi](https://sunila-gollapudi.medium.com/?source=post_page---byline--35b1ae701a4d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--35b1ae701a4d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--35b1ae701a4d--------------------------------) [Sunila Gollapudi](https://sunila-gollapudi.medium.com/?source=post_page---byline--35b1ae701a4d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--35b1ae701a4d--------------------------------) ·17 分钟阅读·2024 年 5 月 29 日

--

检索增强生成（RAG）系统是生成性人工智能和基于检索的技术的高级结合体。RAG 的目的是通过将*外部检索的数据*融入生成过程，来提高生成文本的质量。这一整合的基础是**知识图谱**的应用，它以多种方式显著增强 RAG 的功能。让我们来了解在 RAG 中使用知识图谱的优势：

![](img/4e5cd2776ab77f750b31a11fd43f3197.png)

在 RAG 中使用知识图谱的优势：（作者）

1.  *可靠的领域语料库：* 知识图谱是结构化的数据库，存储并排列有关不同领域实体的事实、关系和语义信息。它们为 RAG 系统提供了一个庞大的、特定领域的语料库，以支持相关数据的检索。

1.  *减轻文本生成中的幻觉现象：* 采用生成性 AI 的一大缺点是‘幻觉’现象的普遍存在——即产生虚假或误导性的信息，伪装成事实。随着…
