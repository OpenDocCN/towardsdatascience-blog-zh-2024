# AI 代理与 AI 管道：编写 LLM 应用程序的实用指南

> 原文：[https://towardsdatascience.com/ai-agents-vs-ai-pipelines-a-practical-guide-to-coding-your-llm-application-167e3c9a654c?source=collection_archive---------0-----------------------#2024-09-27](https://towardsdatascience.com/ai-agents-vs-ai-pipelines-a-practical-guide-to-coding-your-llm-application-167e3c9a654c?source=collection_archive---------0-----------------------#2024-09-27)

## 我们使用 CrewAI 创建应用程序，演示如何为你的 LLM 应用程序选择合适的架构。

[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--167e3c9a654c--------------------------------)[Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--167e3c9a654c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--167e3c9a654c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--167e3c9a654c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--167e3c9a654c--------------------------------) ·阅读时间16分钟·2024年9月27日

--

![](../Images/d1e3e8a9b817c1093887c24be42388e5.png)

*一个代理和一个管道——基于使用Microsoft Bing图像创作者创建的图像*

我们可以把AI代理看作是一个可以访问外部工具的LLM；它在一个循环中运行，每次迭代时都决定如何行动以及使用哪些工具。

通过这样做，代理可以解决比传统 LLM 应用程序更复杂的问题。（我在下面的文章中探讨了如何从零开始构建这样的代理。）

[](https://ai.gopubby.com/how-to-build-a-react-ai-agent-with-claude-3-5-and-python-95423f798640?source=post_page-----167e3c9a654c--------------------------------) [## 如何使用Claude 3.5和Python构建一个ReAct AI代理

### 我们展示了一个Reason+Act代理，它在提供答案之前，反复推理并从外部工具收集信息……

[ai.gopubby.com](https://ai.gopubby.com/how-to-build-a-react-ai-agent-with-claude-3-5-and-python-95423f798640?source=post_page-----167e3c9a654c--------------------------------)

AI 代理是强大的，比传统的聊天应用程序要进步很多。但它们并不总是最合适的解决方案。

有时，更常规的功能顺序可能更合适。我们可以把这种类型的应用程序看作一个管道，通过将一个函数的输出作为下一个函数的输入来开发解决方案。

在本文中，我们将探讨AI代理和管道的使用，以及它们适用于哪些类型的应用程序……
