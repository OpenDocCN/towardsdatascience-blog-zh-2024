# 关于 RAG 的那些事，没有人告诉你

> 原文：[`towardsdatascience.com/what-nobody-tells-you-about-rags-b35f017e1570?source=collection_archive---------0-----------------------#2024-08-23`](https://towardsdatascience.com/what-nobody-tells-you-about-rags-b35f017e1570?source=collection_archive---------0-----------------------#2024-08-23)

## 深入探讨为什么 RAG 并不总是按预期工作：概述其背后的商业价值、数据和技术。

[](https://ahmedbesbes.medium.com/?source=post_page---byline--b35f017e1570--------------------------------)![Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--b35f017e1570--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b35f017e1570--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b35f017e1570--------------------------------) [Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--b35f017e1570--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b35f017e1570--------------------------------) ·阅读时间 11 分钟·2024 年 8 月 23 日

--

![](img/e654c21fcb441774831125c79a5f377d.png)

图片来源：[Lisa Boonaerts](https://unsplash.com/@lisaboonaerts?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

构建一个 RAG（**R**etrieval **A**ugmented **G**eneration，检索增强生成）系统来“与数据对话”其实很简单：安装一个流行的 LLM 编排工具，如 [LangChain](https://python.langchain.com/v0.2/docs/tutorials/rag/) 或 [LlamaIndex](https://docs.llamaindex.ai/en/stable/examples/low_level/oss_ingestion_retrieval/)，将数据转化为向量，在向量数据库中进行索引，并快速设置一个默认提示的管道。

几行代码，就可以收工了。

或者你可能这么认为。

现实比这复杂得多。为了展示效果而简化的原生 RAG 实现，虽然适用于 5 分钟的演示，但在实际业务场景中并不好用。

别误会，那些简短粗糙的演示非常适合理解基础概念。但在实际操作中，要使一个 RAG 系统具备生产能力，不仅仅是将一些代码拼接在一起。这还涉及到应对凌乱的数据、意外的用户查询，以及不断面临的提供实际商业价值的压力。

> *在这篇文章中，我们首先将探讨* ***决定一个基于 RAG 项目成败的商业关键因素***。接下来，我们将深入讨论常见的技术难题——从* ***数据处理*** *到* ***性能优化*** *——并讨论* ***应对策略*** *……
