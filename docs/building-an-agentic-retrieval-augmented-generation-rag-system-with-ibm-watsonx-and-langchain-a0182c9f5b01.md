# 使用 IBM Watsonx 和 Langchain 构建代理型检索增强生成（RAG）系统

> 原文：[`towardsdatascience.com/building-an-agentic-retrieval-augmented-generation-rag-system-with-ibm-watsonx-and-langchain-a0182c9f5b01?source=collection_archive---------8-----------------------#2024-08-23`](https://towardsdatascience.com/building-an-agentic-retrieval-augmented-generation-rag-system-with-ibm-watsonx-and-langchain-a0182c9f5b01?source=collection_archive---------8-----------------------#2024-08-23)

## 快速入门教程

[](https://medium.com/@lakshmi.sunil5486?source=post_page---byline--a0182c9f5b01--------------------------------)![Lakshmi Narayanan](https://medium.com/@lakshmi.sunil5486?source=post_page---byline--a0182c9f5b01--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a0182c9f5b01--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0182c9f5b01--------------------------------) [Lakshmi Narayanan](https://medium.com/@lakshmi.sunil5486?source=post_page---byline--a0182c9f5b01--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0182c9f5b01--------------------------------) ·阅读时间 5 分钟·2024 年 8 月 23 日

--

![](img/82d45062ebfa7ded4e17227bac4a2f07.png)

AI 生成图像（由 GPT-4o 生成）

人工智能（AI）领域，特别是在生成式 AI 方面，最近取得了显著进展。大型语言模型（LLMs）在这方面具有革命性意义。构建 LLM 应用程序的一种流行方法是检索增强生成（RAG），它结合了利用组织数据的能力和这些 LLM 的生成能力。代理是一种流行且有用的方式，可以将自主行为引入 LLM 应用程序中。

**什么是代理型 RAG？**

**代理型 RAG**代表了 AI 系统的一个高级演进，*在这种系统中，自主代理利用 RAG 技术来增强决策和响应能力*。与传统的 RAG 模型不同，后者通常依赖用户输入来触发行动，代理型 RAG 系统采取了主动的方法。这些代理主动寻找相关信息，分析并利用它生成响应或采取具体行动。代理被配备了一套*工具*，并*能够谨慎地选择并使用适当的工具来解决特定问题*。

这种主动行为在许多应用场景中尤为宝贵，如客户服务、研究协助和复杂问题处理等…
