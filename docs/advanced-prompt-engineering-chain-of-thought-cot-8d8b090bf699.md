# 高级提示工程：思维链（CoT）

> 原文：[https://towardsdatascience.com/advanced-prompt-engineering-chain-of-thought-cot-8d8b090bf699?source=collection_archive---------0-----------------------#2024-12-23](https://towardsdatascience.com/advanced-prompt-engineering-chain-of-thought-cot-8d8b090bf699?source=collection_archive---------0-----------------------#2024-12-23)

## 与大型语言模型的工作

## 比较不同的推理技术

[](https://medium.com/@ilsilfverskiold?source=post_page---byline--8d8b090bf699--------------------------------)[![Ida Silfverskiöld](../Images/a2c0850bc0198688f70a5eca858cf8b5.png)](https://medium.com/@ilsilfverskiold?source=post_page---byline--8d8b090bf699--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8d8b090bf699--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8d8b090bf699--------------------------------) [Ida Silfverskiöld](https://medium.com/@ilsilfverskiold?source=post_page---byline--8d8b090bf699--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d8b090bf699--------------------------------) ·阅读时间：20分钟·2024年12月23日

--

![](../Images/9ac39b3dac5c2c00c2dd3b8e1b3216a4.png)

一些推理技术的不同形式 | 作者图片

*如果你不是会员，但想阅读本文，请查看这个朋友链接* [*这里*](https://medium.com/@ilsilfverskiold/8d8b090bf699?sk=f6afe11542fb309fe1260863e63d4a26)

思维链（CoT）已经存在了一段时间，技术上它是一种高级提示工程的形式，但即使在其首次出现几年后，它仍然保持着相关性。CoT在其不同的形式中，通常是试图迫使大型语言模型进行推理。

在OpenAI于今年九月发布他们的o1模型预览后，我们看到围绕思维链（CoT）的热度增加。

没有人完全知道o1是如何工作的（除OpenAI外），它是否是一个组合系统，使用了什么样的数据进行微调，是否使用了强化学习，或者是否有多个模型协同工作。

*也许一个模型负责规划，另一个负责思考，第三个负责评分。但我们知道它们确实在使用某种逐步推理的方式。*

关于这一点有很多公开的研究，或许你会想深入了解。因此，本文我将概述目前已有的研究，以便你知道哪些技术可以使用。自然地，我会测试不同的技术，看看它们的效果……
