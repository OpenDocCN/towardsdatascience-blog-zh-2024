# 多模态模型 — 可以“看”和“听”的 LLM

> 原文：[`towardsdatascience.com/multimodal-models-llms-that-can-see-and-hear-5c6737c981d3?source=collection_archive---------7-----------------------#2024-11-19`](https://towardsdatascience.com/multimodal-models-llms-that-can-see-and-hear-5c6737c981d3?source=collection_archive---------7-----------------------#2024-11-19)

## 包含示例 Python 代码的介绍

[](https://shawhin.medium.com/?source=post_page---byline--5c6737c981d3--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--5c6737c981d3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5c6737c981d3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5c6737c981d3--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--5c6737c981d3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5c6737c981d3--------------------------------) ·阅读时长 9 分钟·2024 年 11 月 19 日

--

这是关于[多模态 AI](https://shawhin.medium.com/list/multimodal-ai-fe9521d0e77a)系列文章的第一篇。**多模态模型 (MM)** 是一种**能够处理或生成多种数据模态的 AI 系统**（例如文本、图像、音频、视频）。在本文中，我将讨论一种特殊类型的 MM，它建立在大型语言模型 (LLM) 的基础上。我将从高层次概述此类模型开始，然后分享使用 LLaMA 3.2 Vision 执行各种图像到文本任务的示例代码。

![](img/27ab3af36cfa4d6886cc5244b94147db.png)

图片由[Sincerely Media](https://unsplash.com/@sincerelymedia?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

大型语言模型 (LLMs)在人工智能的研究与开发中标志着一次根本性的转变。然而，尽管它们的影响广泛，仍然**存在根本的局限性**。

具体来说，LLMs 只能处理和生成文本，这使得它们对图像、视频、音频等其他模态*视而不见*。这是一个主要的限制，因为**一些任务依赖于非文本数据**，例如分析工程图纸、解读肢体语言或语音语调，以及解释图表和信息图。
