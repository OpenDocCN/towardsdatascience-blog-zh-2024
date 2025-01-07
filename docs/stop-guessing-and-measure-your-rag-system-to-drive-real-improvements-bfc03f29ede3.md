# 停止猜测，衡量你的 RAG 系统以推动真正的改进

> 原文：[`towardsdatascience.com/stop-guessing-and-measure-your-rag-system-to-drive-real-improvements-bfc03f29ede3?source=collection_archive---------1-----------------------#2024-10-04`](https://towardsdatascience.com/stop-guessing-and-measure-your-rag-system-to-drive-real-improvements-bfc03f29ede3?source=collection_archive---------1-----------------------#2024-10-04)

## 提升检索增强生成（RAG）性能的关键指标和技巧

[](https://medium.com/@abhinavkimothi?source=post_page---byline--bfc03f29ede3--------------------------------)![Abhinav Kimothi](https://medium.com/@abhinavkimothi?source=post_page---byline--bfc03f29ede3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bfc03f29ede3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bfc03f29ede3--------------------------------) [Abhinav Kimothi](https://medium.com/@abhinavkimothi?source=post_page---byline--bfc03f29ede3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bfc03f29ede3--------------------------------) ·阅读时间：24 分钟·2024 年 10 月 4 日

--

大型语言模型（LLMs）的进展引起了全世界的关注。随着[OpenAI 发布的 ChatGPT](https://chatgpt.com/)在 2022 年 11 月上线，以前鲜为人知的术语如生成式人工智能（Generative AI）进入了公众话语。短短时间内，LLMs 在现代语言处理任务中找到了广泛的[应用](https://pixelplex.io/blog/llm-applications/)，甚至为[自主 AI 代理](https://www.promptingguide.ai/research/llm-agents)铺平了道路。有些人称之为技术的分水岭时刻，并将其与互联网的到来，甚至是电灯泡的发明做出崇高的比较。因此，绝大多数商业领袖、软件开发人员和企业家都在热衷于利用 LLMs 为自己带来优势。

检索增强生成（RAG）作为塑造应用生成式人工智能领域的一个关键技术，正在发挥重要作用。RAG 是 Lewis 等人在其开创性论文[*Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*](https://arxiv.org/abs/2005.11401)中提出的一个新概念，它迅速成为增强大型语言模型输出可靠性和可信度的基石。

在这篇博客文章中，我们将深入探讨如何评估 RAG 系统。但在此之前，让我们通过理解 RAG 的需求并概述 RAG 管道的实施来设定背景。

# LLMs 的诅咒
