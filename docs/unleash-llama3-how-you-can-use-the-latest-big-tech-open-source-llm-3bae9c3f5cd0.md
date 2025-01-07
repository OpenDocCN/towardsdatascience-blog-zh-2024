# 解锁 Llama3 —— 如何使用最新的大型科技开源 LLM

> 原文：[`towardsdatascience.com/unleash-llama3-how-you-can-use-the-latest-big-tech-open-source-llm-3bae9c3f5cd0?source=collection_archive---------7-----------------------#2024-04-26`](https://towardsdatascience.com/unleash-llama3-how-you-can-use-the-latest-big-tech-open-source-llm-3bae9c3f5cd0?source=collection_archive---------7-----------------------#2024-04-26)

## 本文讨论了 Llama3，这是 Meta 发布的最新语言模型，并介绍了如何在本地运行该模型，同时也分享了我对该模型性能的看法。

[](https://oieivind.medium.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------)

· 发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3bae9c3f5cd0--------------------------------) · 12 分钟阅读 · 2024 年 4 月 26 日

--

Llama3 是 Meta 的 AI 团队发布的最新模型。根据[Meta 关于 Llama3 的博客](https://ai.meta.com/blog/meta-llama-3/)，Llama3 在 63.2%的情况下在指令性人工评估上超越了 GPT3.5。根据这一衡量标准，Llama3 在典型的 LLM 任务中的表现应当超越 GPT3.5。在本文中，我将向您展示一种简单的方法，帮助您在计算机上运行 Llama3，进行各种测试以了解其能力，并最终分享我对该模型的看法。

![](img/f1ab8ed9e06d7582dadd9ec5d394d633.png)

3 只代表 Llama 3 的美洲驼。图片来源：ChatGPT。OpenAI。（2024 年）。*ChatGPT*（4）[大型语言模型]。[`chat.openai.com`](https://chat.openai.com)

# 目录

· 目录

· 动机

· 在本地实现该模型

· 测试 Llama3

∘ 测试 Llamas 提供简洁答案的能力

∘ 测试 Llamas 返回正确格式化对象的能力

∘ 测试信息提取能力

∘ 测试 Llama 的编码能力

· 我对该模型的看法

· 结论
