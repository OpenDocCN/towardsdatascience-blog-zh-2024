# AutoRound：LLMs 的准确低比特量化

> 原文：[`towardsdatascience.com/autoround-accurate-low-bit-quantization-for-llms-305ddb38527a?source=collection_archive---------0-----------------------#2024-06-29`](https://towardsdatascience.com/autoround-accurate-low-bit-quantization-for-llms-305ddb38527a?source=collection_archive---------0-----------------------#2024-06-29)

## 在量化感知训练和后训练量化之间

[](https://medium.com/@bnjmn_marie?source=post_page---byline--305ddb38527a--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--305ddb38527a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--305ddb38527a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--305ddb38527a--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--305ddb38527a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--305ddb38527a--------------------------------) ·7 分钟阅读·2024 年 6 月 29 日

--

![](img/72daa1eec27a277be9f6d145f7e5d5d9.png)

由 DALL-E 生成

有许多量化方法可以减少大型语言模型（LLM）的大小。最近，已经提出了更好的低比特量化方法。例如，AQLM 在保留大部分模型准确性的同时实现了 2 比特量化。

[AQLM](https://medium.com/towards-artificial-intelligence/fine-tune-mixtral-8x7b-quantized-with-aqlm-2-bit-on-your-gpu-4f8fac86e523)的主要缺点是，量化大型模型的成本非常高。[HQQ](https://github.com/mobiusml/hqq)是另一个低比特量化的良好替代方案，但需要进一步的微调才能保持准确性。

英特尔在更好的量化算法研究方面也非常活跃。他们提出了 AutoRound，一种采用符号梯度下降（SignSD）的新量化方法。AutoRound 在低比特量化方面特别准确，并且比大多数其他方法量化得更快。

在本文中，我回顾了 AutoRound。我们将了解它的工作原理以及如何以最小的准确性下降量化 LLM，例如 Llama 3。我发现 AutoRound 是 GPTQ 和 HQQ 的一个非常好的替代方案。它能产生更准确的模型。

我实现了以下笔记本，展示了如何使用 AutoRound 对大型语言模型（LLMs）进行量化，并评估/基准测试结果模型：
