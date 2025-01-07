# 引导大型语言模型（LLM）的响应以创建结构化输出

> 原文：[`towardsdatascience.com/guiding-an-llms-response-to-create-structured-output-5dde0d3e426b?source=collection_archive---------5-----------------------#2024-06-21`](https://towardsdatascience.com/guiding-an-llms-response-to-create-structured-output-5dde0d3e426b?source=collection_archive---------5-----------------------#2024-06-21)

## *学习如何构建语言模型的响应，确保响应格式被遵守，如 JSON*

[](https://medium.com/@theDrewDag?source=post_page---byline--5dde0d3e426b--------------------------------)![Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--5dde0d3e426b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5dde0d3e426b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5dde0d3e426b--------------------------------) [Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--5dde0d3e426b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5dde0d3e426b--------------------------------) ·阅读时间 9 分钟·2024 年 6 月 21 日

--

![](img/46656d56b1953f990b95d596d65622a0.png)

图片来源：[Ricardo Gomez Angel](https://unsplash.com/@rgaleriacom?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文将教您如何使用 Python 中的验证库来构建 LLM 响应，如 GPT-4 或 Llama 3。

这是一个非常相关的话题，因为**从无结构格式（如自由文本）中提取精确信息，转化为 JSON 格式的数据，已被证明在数据挖掘任务中至关重要**。

此外，由于大型语言模型（LLM）生成输出令牌的随机特性，即使是在最商业化的系统中，如 GPT，结构化响应格式也不可靠。

我们将使用多个库，如 Pydantic 和 Instructor 进行验证和模式建模，OpenAI 和 ollama 用于 LLM 部分。所提出的内容适用于封闭源模型，如 OpenAI 或 Anthropic 的 GPT，以及开源模型，如 Llama 3。

阅读本文后，您将学习到：

+   什么是**数据模型**，以及如何定义它

+   如何确保您的**LLM 通过验证规则遵守输出格式**
