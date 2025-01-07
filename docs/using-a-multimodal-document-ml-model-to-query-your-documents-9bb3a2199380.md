# 使用多模态文档 ML 模型查询您的文档

> 原文：[`towardsdatascience.com/using-a-multimodal-document-ml-model-to-query-your-documents-9bb3a2199380?source=collection_archive---------4-----------------------#2024-04-11`](https://towardsdatascience.com/using-a-multimodal-document-ml-model-to-query-your-documents-9bb3a2199380?source=collection_archive---------4-----------------------#2024-04-11)

## 利用 mPLUG-Owl 文档理解模型的强大功能来向您的文档提问

[](https://oieivind.medium.com/?source=post_page---byline--9bb3a2199380--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--9bb3a2199380--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9bb3a2199380--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9bb3a2199380--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--9bb3a2199380--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9bb3a2199380--------------------------------) ·阅读时长 9 分钟·2024 年 4 月 11 日

--

本文将讨论最近发布的[阿里巴巴文档理解模型](https://github.com/X-PLUG/mPLUG-DocOwl?tab=readme-ov-file)，该模型包含了模型权重和数据集。它是一个强大的模型，能够执行多种任务，如文档问答、信息提取和文档嵌入，使其成为处理文档时的一个有用工具。本文将本地实现该模型，并在不同任务上进行测试，以便对其性能和实用性进行评价。

![](img/6eacf9e9ef1a87479c46ce4dcbcd3232.png)

本文将讨论文档理解领域的最新模型。图片由 ChatGPT 提供。OpenAI。（2024）。*ChatGPT*（4）[大型语言模型]。[`chat.openai.com`](https://chat.openai.com)

# 目录

· 动机

· 任务

· 本地运行模型

· 模型测试

∘ 数据

∘ 测试第二个，最右侧的收据：

∘ 测试第二个，最右侧的收据：

∘ 测试第一个，最左侧的讲义笔记：

∘ 测试第二个，最右侧的讲义笔记

· 我对该模型的想法

· 结论

# 动机
