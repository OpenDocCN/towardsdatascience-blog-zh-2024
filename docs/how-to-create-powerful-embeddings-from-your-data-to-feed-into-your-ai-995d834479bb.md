# 如何从您的数据中创建强大的嵌入以供 AI 使用

> 原文：[`towardsdatascience.com/how-to-create-powerful-embeddings-from-your-data-to-feed-into-your-ai-995d834479bb?source=collection_archive---------0-----------------------#2024-02-21`](https://towardsdatascience.com/how-to-create-powerful-embeddings-from-your-data-to-feed-into-your-ai-995d834479bb?source=collection_archive---------0-----------------------#2024-02-21)

## 本文将展示您可以采取的不同方法来为您的数据创建嵌入

[](https://oieivind.medium.com/?source=post_page---byline--995d834479bb--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--995d834479bb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--995d834479bb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--995d834479bb--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--995d834479bb--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--995d834479bb--------------------------------) ·14 分钟阅读·2024 年 2 月 21 日

--

从您的数据中创建高质量的嵌入对于您的 AI 系统的有效性至关重要。本文将展示您可以采用的不同方法，将图像、文本和音频等格式的数据转换为强大的嵌入，这些嵌入可以用于您的机器学习任务。您创建高性能嵌入的能力将对 AI 系统的性能产生重大影响，因此学习和理解如何打造优质嵌入至关重要。

![](img/65ab45599aaafba31d42a63d9f9bf01d.png)

从一张照片中制作嵌入。图片由 ChatGPT 提供。 “制作一张展示 AI 从照片中制作嵌入的图片”提示。*ChatGPT*，4，OpenAI，2024 年 2 月 18 日。[`chat.openai.com.`](https://chat.openai.com.)

# 简介

本文的动机在于，从您的数据中创建良好的嵌入对大多数 AI 系统至关重要，因此它是您经常需要做的事情，制作更好的嵌入是提升您所有未来 AI 系统的有效方法。创建嵌入的应用场景包括聚类、相似性搜索和异常检测，所有这些任务都可以通过更好的嵌入大大受益。本文将探讨两种主要的嵌入计算方法：使用在线模型或训练您自己的模型，接下来的部分将详细讨论这两种方法。
