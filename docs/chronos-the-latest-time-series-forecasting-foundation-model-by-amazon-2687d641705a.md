# Chronos：亚马逊最新的时间序列预测基础模型

> 原文：[`towardsdatascience.com/chronos-the-latest-time-series-forecasting-foundation-model-by-amazon-2687d641705a?source=collection_archive---------1-----------------------#2024-03-27`](https://towardsdatascience.com/chronos-the-latest-time-series-forecasting-foundation-model-by-amazon-2687d641705a?source=collection_archive---------1-----------------------#2024-03-27)

## 深入了解 Chronos 及其内部工作原理，并学习如何使用 Python 将其应用于您的预测项目。

[](https://medium.com/@marcopeixeiro?source=post_page---byline--2687d641705a--------------------------------)![Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--2687d641705a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2687d641705a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2687d641705a--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--2687d641705a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2687d641705a--------------------------------) ·12 分钟阅读·2024 年 3 月 27 日

--

![](img/050834eef7bd171d9d42c62fffb33264.png)

图片由[sutirta budiman](https://unsplash.com/@sutirtab?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

时间序列预测领域最近充满活力，许多工作正在进行，以开发基础预测模型。

一切始于 2023 年 10 月，发布了 TimeGPT，它是第一个能够进行零-shot 预测和异常检测的基础模型之一。

随后，许多努力也致力于将 LLM 适应于预测任务，如 PromptCast 和 LLMTime。

此后，我们看到了更多开源的基础模型，如用于概率零-shot 预测的 Lag-LLaMA 和重新编程现有现成语言模型进行时间序列预测的 Time-LLM。

现在，在 2024 年 3 月，亚马逊公司也加入了这一行列，发布了**Chronos**。

在他们的论文《[Chronos: 学习时间序列语言](https://arxiv.org/pdf/2403.07815.pdf)》中，作者提出了一种零-shot 概率预测框架，利用现有的基于 Transformer 的语言模型架构。它可以最小化地适应现有的语言模型用于预测任务。
