# 通过复杂推理提高 RAG 回答质量

> 原文：[`towardsdatascience.com/improving-rag-answer-quality-through-complex-reasoning-2608ec6c2a65?source=collection_archive---------3-----------------------#2024-07-24`](https://towardsdatascience.com/improving-rag-answer-quality-through-complex-reasoning-2608ec6c2a65?source=collection_archive---------3-----------------------#2024-07-24)

## 展示使用 DSPy 和 Indexify 构建多跳检索系统的过程

[](https://sachinkhandewal.medium.com/?source=post_page---byline--2608ec6c2a65--------------------------------)![Sachin Khandewal](https://sachinkhandewal.medium.com/?source=post_page---byline--2608ec6c2a65--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2608ec6c2a65--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2608ec6c2a65--------------------------------) [Sachin Khandewal](https://sachinkhandewal.medium.com/?source=post_page---byline--2608ec6c2a65--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2608ec6c2a65--------------------------------) ·阅读时间：25 分钟·2024 年 7 月 24 日

--

# TLDR;

+   在本文中，我们将探索多跳检索及其如何被利用来构建需要复杂推理的 RAG 系统。

+   我们将通过在医疗领域构建一个 Q&A 聊天机器人，使用 Indexify、OpenAI 和 DSPy，来展示这一技术。

+   多跳思维链 RAG 如何高效回答复杂问题。

# 引言

检索增强生成（RAG）系统已经成为构建 LLM（大语言模型）驱动应用程序的强大方法。RAG 系统首先通过检索模型从外部知识源中检索信息，然后利用这些信息来提示 LLM 生成响应。

然而，基础的 RAG 系统（也称为简单 RAG）在处理需要对多个信息片段进行推理的复杂查询时可能会面临挑战。这时，多跳检索就发挥了作用。

在多跳检索中，系统通过多个步骤或“跳跃”收集信息，以回答复杂问题或收集详细信息。这种技术在高级问答系统中很常见，其中多个来源或文档包含回答问题所需的信息。
