# 多模态嵌入：介绍

> 原文：[https://towardsdatascience.com/multimodal-embeddings-an-introduction-5dc36975966f?source=collection_archive---------4-----------------------#2024-11-29](https://towardsdatascience.com/multimodal-embeddings-an-introduction-5dc36975966f?source=collection_archive---------4-----------------------#2024-11-29)

## 将文本和图像映射到一个共同的空间

[](https://shawhin.medium.com/?source=post_page---byline--5dc36975966f--------------------------------)[![Shaw Talebi](../Images/1449cc7c08890e2078f9e5d07897e3df.png)](https://shawhin.medium.com/?source=post_page---byline--5dc36975966f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5dc36975966f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5dc36975966f--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--5dc36975966f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5dc36975966f--------------------------------) ·阅读时长8分钟·2024年11月29日

--

这是[更大系列](https://shawhin.medium.com/list/multimodal-ai-fe9521d0e77a)中关于多模态AI的第二篇文章。在[上一篇文章](/multimodal-models-llms-that-can-see-and-hear-5c6737c981d3)中，我们了解了如何增强[大型语言模型（LLMs）](https://shawhin.medium.com/list/large-language-models-llms-8e009ae3054c)以理解新的数据模态（例如图像、音频、视频）。其中一种方法依赖于编码器来生成非文本数据的向量表示（即嵌入）。在本文中，我将讨论*多模态*嵌入，并通过两个实际的使用案例展示它们的功能。

![](../Images/8c2812bed48f472f72cfe64d7de7b9a9.png)

图像来自Canva。

人工智能研究传统上分为不同的领域：自然语言处理（NLP）、计算机视觉（CV）、机器人学、人机交互（HCI）等。然而，许多实际任务需要**整合这些不同的研究领域**，例如自动驾驶汽车（CV + 机器人学）、AI代理（NLP + CV + HCI）、个性化学习（NLP + HCI）等。

尽管这些领域旨在解决不同的问题并处理不同类型的数据，但它们都共享一个基本过程。即**生成现实世界现象的有用数值表示**。
