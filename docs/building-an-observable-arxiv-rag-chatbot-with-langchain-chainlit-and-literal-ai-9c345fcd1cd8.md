# 使用 LangChain、Chainlit 和 Literal AI 构建一个可观察的 arXiv RAG 聊天机器人

> 原文：[https://towardsdatascience.com/building-an-observable-arxiv-rag-chatbot-with-langchain-chainlit-and-literal-ai-9c345fcd1cd8?source=collection_archive---------2-----------------------#2024-05-13](https://towardsdatascience.com/building-an-observable-arxiv-rag-chatbot-with-langchain-chainlit-and-literal-ai-9c345fcd1cd8?source=collection_archive---------2-----------------------#2024-05-13)

## 使用 RAG 和 LangChain、Chainlit Copilot 应用程序以及 Literal AI 可观察性构建语义论文引擎的教程。

[](https://medium.com/@tahreemrasul?source=post_page---byline--9c345fcd1cd8--------------------------------)[![Tahreem Rasul](../Images/fbec150343ddb89ee07168e261595f8e.png)](https://medium.com/@tahreemrasul?source=post_page---byline--9c345fcd1cd8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9c345fcd1cd8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9c345fcd1cd8--------------------------------) [Tahreem Rasul](https://medium.com/@tahreemrasul?source=post_page---byline--9c345fcd1cd8--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9c345fcd1cd8--------------------------------) ·阅读时长 17 分钟·2024年5月13日

--

在本教程中，我将演示如何使用检索增强生成（RAG）构建一个语义研究论文引擎。我将利用[LangChain](https://www.langchain.com)作为构建我们语义引擎的主要框架，同时使用[OpenAI](https://openai.com)的语言模型和[Chroma DB](https://www.trychroma.com)的向量数据库。为了构建嵌入 Copilot 的 Web 应用程序，我将使用[Chainlit](https://docs.chainlit.io/get-started/overview)的 Copilot 功能，并结合[Literal AI](https://literalai.com)的可观察性特性。这个工具可以通过简化查找相关论文的过程，促进学术研究。用户还可以通过询问推荐的论文问题，直接与内容互动。最后，我们将在应用程序中集成可观察性特性，以跟踪和调试对大语言模型（LLM）的调用。

![](../Images/bf3478156385c7735128a9f01bb91420.png)

嵌入 Copilot 的语义研究论文应用程序的应用架构。作者插图。

下面是我们将在本教程中涵盖的所有内容概述：

+   开发一个 RAG 流水线，利用 OpenAI、LangChain 和 Chroma DB 处理和检索来自 arXiv API 的最相关 PDF 文档。

+   开发一个带有 Copilot 的 Chainlit 应用程序，用于在线论文检索。

+   提升应用程序……
