# 如何构建一个 LLM 驱动的应用程序与 PapersWithCode 聊天

> 原文：[`towardsdatascience.com/how-to-build-an-llm-powered-app-to-chat-with-paperswithcode-09ddd9ee753a?source=collection_archive---------0-----------------------#2024-02-01`](https://towardsdatascience.com/how-to-build-an-llm-powered-app-to-chat-with-paperswithcode-09ddd9ee753a?source=collection_archive---------0-----------------------#2024-02-01)

## 跟上最新的机器学习研究进展

[](https://ahmedbesbes.medium.com/?source=post_page---byline--09ddd9ee753a--------------------------------)![Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--09ddd9ee753a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--09ddd9ee753a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09ddd9ee753a--------------------------------) [Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--09ddd9ee753a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09ddd9ee753a--------------------------------) ·阅读时间：11 分钟·2024 年 2 月 1 日

--

![](img/ba17bc9a2c7daa08883a672d18e1e2fe.png)

图片来自[Patrick Tomasso](https://unsplash.com/@impatrickt?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

您是否觉得跟不上最新的机器学习研究进展？是否被大量关于 LLM、向量数据库或 RAG 的论文压得喘不过气来？

> ***在这篇文章中，我将展示如何构建一个 AI 助手，轻松挖掘大量信息。您将以自然语言向它提问，它会根据找到的相关论文回答您的问题，论文来自***[***Papers With Code***](https://paperswithcode.com/)***。***

在后端方面，这个助手将由一个基于可扩展无服务器向量数据库、来自 VertexAI 的嵌入模型和来自 OpenAI 的 LLM 的检索增强生成（RAG）框架提供支持。

在前端方面，助手将集成到一个互动式且易于部署的 Web 应用程序中，该程序是使用[Streamlit](https://streamlit.io/)构建的。

本文将详细介绍此过程的每个步骤，并提供一个您可以重用和调整的源代码👇。

准备好了吗？让我们开始吧🔍。

> 如果您对机器学习内容、详细教程以及来自行业的实用技巧感兴趣，请关注我的[新闻通讯](https://thetechbuffet.substack.com/)。它叫做***The Tech Buffet.***
