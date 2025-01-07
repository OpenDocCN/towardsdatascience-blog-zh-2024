# 微调 Llama 3 的微型适配器与 VeRA

> 原文：[`towardsdatascience.com/fine-tune-tiny-adapters-for-llama-3-with-vera-7c48f4391d84?source=collection_archive---------8-----------------------#2024-06-11`](https://towardsdatascience.com/fine-tune-tiny-adapters-for-llama-3-with-vera-7c48f4391d84?source=collection_archive---------8-----------------------#2024-06-11)

## LoRA，但小巧 100 倍

[](https://medium.com/@bnjmn_marie?source=post_page---byline--7c48f4391d84--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--7c48f4391d84--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7c48f4391d84--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7c48f4391d84--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--7c48f4391d84--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7c48f4391d84--------------------------------) ·6 分钟阅读·2024 年 6 月 11 日

--

![](img/6fbba9c193cdb65c717fbb23bbe12ae4.png)

由 DALL-E 生成

LoRA 通过在预训练的 LLM（大语言模型）上添加一个适配器来进行微调，只有这个适配器是可训练的，而 LLM 的原始参数保持冻结。这种方法显著减少了需要训练的参数数量，从而大大缩小了优化器的状态。因此，与标准的完全微调相比，LoRA 微调消耗的内存大大减少。

然而，根据 LoRA 的超参数，如秩和目标模块的数量，LoRA 可能仍会创建具有数亿个参数的非常大的适配器，这些适配器太大，无法在消费者硬件上进行微调。

已经提出了许多替代方案来减少适配器的大小。

在本文中，我回顾了 VeRA，这是一种将适配器缩小 100 倍的 LoRA 替代方案。我使用 VeRA 对 Llama 3 进行了微调，并将其性能与 LoRA 进行了比较。

# VeRA：基于随机矩阵的向量微调

VeRA 在本文中提出：

[VeRA：基于随机矩阵的向量适配](https://arxiv.org/abs/2310.11454)

*注意：这是我最喜欢的论文之一，展示了 LoRA 的替代方案。它写得非常好*…
