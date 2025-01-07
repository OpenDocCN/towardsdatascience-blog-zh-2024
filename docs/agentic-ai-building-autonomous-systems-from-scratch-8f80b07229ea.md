# 代理式 AI：从零开始构建自主系统

> 原文：[https://towardsdatascience.com/agentic-ai-building-autonomous-systems-from-scratch-8f80b07229ea?source=collection_archive---------0-----------------------#2024-12-13](https://towardsdatascience.com/agentic-ai-building-autonomous-systems-from-scratch-8f80b07229ea?source=collection_archive---------0-----------------------#2024-12-13)

## 生成性 AI 时代构建多代理框架的逐步指南

[](https://medium.com/@luisroque?source=post_page---byline--8f80b07229ea--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--8f80b07229ea--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8f80b07229ea--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8f80b07229ea--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--8f80b07229ea--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8f80b07229ea--------------------------------) ·阅读时长 13分钟·2024年12月13日

--

*本文由 Rafael Guedes 共同撰写。*

# 引言

生成性 AI 的兴起是数字时代的新平台变革。它解决了从大型企业自动化到各种类型的研发和创造力等问题。预计全球市场将在2024年超过650亿美元，86%的IT领导者预计会出现大规模的组织变革[1]。到目前为止，最大的回报来自聊天机器人（更为通用和广泛的应用场景）、代码助手和企业搜索。

投资仍在持续流入AI领域，2024年投资额达到138亿美元（是2023年的六倍）[1]。此外，企业正将AI嵌入到它们的核心战略和系统中。像增强检索生成（RAG）、微调和针对垂直应用的专用模型（例如，医疗保健、法律）等技术正成为主流。

大型语言模型（LLMs）引起了人们对AI的关注（以多种方式），并为解决旧问题开辟了新途径。这个新方式就是通过代理式 AI —— 一个框架，其中自主代理协同工作以执行复杂的多步骤工作流。

我们的演示展示了如何构建并开发一个**多代理系统**。它整合了三个专门化的代理：

+   一种**网页研究代理**……
