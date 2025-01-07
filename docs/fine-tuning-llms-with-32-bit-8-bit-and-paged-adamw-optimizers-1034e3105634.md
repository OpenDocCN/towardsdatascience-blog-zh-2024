# 使用 32 位、8 位和分页 AdamW 优化器微调 LLM

> 原文：[`towardsdatascience.com/fine-tuning-llms-with-32-bit-8-bit-and-paged-adamw-optimizers-1034e3105634?source=collection_archive---------9-----------------------#2024-10-10`](https://towardsdatascience.com/fine-tuning-llms-with-32-bit-8-bit-and-paged-adamw-optimizers-1034e3105634?source=collection_archive---------9-----------------------#2024-10-10)

## 寻找内存效率、准确性和速度之间的最佳权衡

[](https://medium.com/@bnjmn_marie?source=post_page---byline--1034e3105634--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--1034e3105634--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1034e3105634--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1034e3105634--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--1034e3105634--------------------------------)

·发布在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1034e3105634--------------------------------) ·7 分钟阅读·2024 年 10 月 10 日

--

![](img/812b382ba4f0564091b02974dfb093d7.png)

使用 Grok 生成

微调大型语言模型（LLM）已经成为一项必要但资源密集型的任务，尤其是在使用 AdamW 优化器时，因为它会迅速消耗可用的资源。对于每个模型参数，AdamW 需要在内存中存储两个额外的优化器状态，每个状态通常采用 float32 格式。这意味着每个参数需要额外的 8 字节内存，对于像 Llama 3.1 这样拥有 80 亿个参数的模型，仅用于管理优化器状态的内存就大约需要 64GB。

使用量化和分页优化器可以显著减少内存开销。像 bitsandbytes 这样的库促进了这些内存高效的做法，使其越来越受欢迎。

在本文中，我们将对 AdamW-32 位、其 8 位对应物和分页 AdamW 优化器进行比较分析，研究它们对内存消耗、学习曲线和训练时间的影响。我们的目标是识别何时需要内存高效的优化器，并评估它们在训练速度和模型准确性之间的权衡。在第一部分，我们将回顾 AdamW 8 位及其分页变体。然后，我们将进行基准测试……
