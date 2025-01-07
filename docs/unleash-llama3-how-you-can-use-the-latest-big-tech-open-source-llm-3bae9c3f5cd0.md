# 解锁Llama3 —— 如何使用最新的大型科技开源LLM

> 原文：[https://towardsdatascience.com/unleash-llama3-how-you-can-use-the-latest-big-tech-open-source-llm-3bae9c3f5cd0?source=collection_archive---------7-----------------------#2024-04-26](https://towardsdatascience.com/unleash-llama3-how-you-can-use-the-latest-big-tech-open-source-llm-3bae9c3f5cd0?source=collection_archive---------7-----------------------#2024-04-26)

## 本文讨论了Llama3，这是Meta发布的最新语言模型，并介绍了如何在本地运行该模型，同时也分享了我对该模型性能的看法。

[](https://oieivind.medium.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)

· 发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------) · 12分钟阅读 · 2024年4月26日

--

Llama3是Meta的AI团队发布的最新模型。根据[Meta关于Llama3的博客](https://ai.meta.com/blog/meta-llama-3/)，Llama3在63.2%的情况下在指令性人工评估上超越了GPT3.5。根据这一衡量标准，Llama3在典型的LLM任务中的表现应当超越GPT3.5。在本文中，我将向您展示一种简单的方法，帮助您在计算机上运行Llama3，进行各种测试以了解其能力，并最终分享我对该模型的看法。

![](../Images/f1ab8ed9e06d7582dadd9ec5d394d633.png)

3只代表Llama 3的美洲驼。图片来源：ChatGPT。OpenAI。（2024年）。*ChatGPT*（4）[大型语言模型]。[https://chat.openai.com](https://chat.openai.com)

# 目录

· [目录](#0f2f)

· [动机](#24ea)

· [在本地实现该模型](#9022)

· [测试Llama3](#00e7)

∘ [测试Llamas提供简洁答案的能力](#020d)

∘ [测试Llamas返回正确格式化对象的能力](#7e97)

∘ [测试信息提取能力](#5257)

∘ [测试Llama的编码能力](#4f0d)

· [我对该模型的看法](#ff4a)

· [结论](#e1a7)
