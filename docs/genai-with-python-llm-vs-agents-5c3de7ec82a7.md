# GenAI 与 Python：LLM 与智能代理

> 原文：[`towardsdatascience.com/genai-with-python-llm-vs-agents-5c3de7ec82a7?source=collection_archive---------1-----------------------#2024-07-11`](https://towardsdatascience.com/genai-with-python-llm-vs-agents-5c3de7ec82a7?source=collection_archive---------1-----------------------#2024-07-11)

## 创建一个 AI 小队，在不需要 GPU 的情况下自动化你笔记本电脑上的几乎所有任务

[](https://maurodp.medium.com/?source=post_page---byline--5c3de7ec82a7--------------------------------)![Mauro Di Pietro](https://maurodp.medium.com/?source=post_page---byline--5c3de7ec82a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5c3de7ec82a7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5c3de7ec82a7--------------------------------) [Mauro Di Pietro](https://maurodp.medium.com/?source=post_page---byline--5c3de7ec82a7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5c3de7ec82a7--------------------------------) ·阅读时长 11 分钟·2024 年 7 月 11 日

--

在本文中，我将展示如何本地构建智能代理，这是生成性人工智能（GenAI）最前沿的技术，并解释它与普通 LLM 的区别。

![](img/87e1d6b260450a133ade50ad0a529396.png)

图片来自[Jeffery Ho](https://unsplash.com/@jefferyho?utm_source=medium&utm_medium=referral)，发布于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

一般来说，[**智能代理**](https://en.wikipedia.org/wiki/Intelligent_agent)是具有足够理解力的实体，能够感知环境，自动采取行动以实现特定目标，并通过获取知识不断改善（像人类一样）。

在技术领域，**AI 代理**是一个自主程序，执行任务、做出决策并与其他实体进行沟通。通常，智能代理会被提供一组工具，它们可以在完成任务时决定是否使用这些工具。这个概念扩展了强化学习，其中代理从一组预定义的行动中选择，以最大化在互动环境中的奖励。

[**大语言模型**（**LLM**）](https://en.wikipedia.org/wiki/Large_language_model)不是智能代理。LLM 是利用[词嵌入](https://en.wikipedia.org/wiki/Word_embedding)和[变压器架构](https://en.wikipedia.org/wiki/Transformer_(deep_learning_architecture))进行高级自然语言处理的神经网络。它们对人类语言有深刻理解，但在其知识库之外不执行任何操作。

在 GenAI 中，**智能代理**是用于处理顺序推理的人工智能系统，具有执行外部工具（例如数据库查询、网页等）的选项。
