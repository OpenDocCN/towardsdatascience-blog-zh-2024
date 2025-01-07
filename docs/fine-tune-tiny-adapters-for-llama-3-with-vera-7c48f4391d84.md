# 微调Llama 3的微型适配器与VeRA

> 原文：[https://towardsdatascience.com/fine-tune-tiny-adapters-for-llama-3-with-vera-7c48f4391d84?source=collection_archive---------8-----------------------#2024-06-11](https://towardsdatascience.com/fine-tune-tiny-adapters-for-llama-3-with-vera-7c48f4391d84?source=collection_archive---------8-----------------------#2024-06-11)

## LoRA，但小巧100倍

[](https://medium.com/@bnjmn_marie?source=post_page---byline--7c48f4391d84--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--7c48f4391d84--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7c48f4391d84--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7c48f4391d84--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--7c48f4391d84--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7c48f4391d84--------------------------------) ·6分钟阅读·2024年6月11日

--

![](../Images/6fbba9c193cdb65c717fbb23bbe12ae4.png)

由DALL-E生成

LoRA通过在预训练的LLM（大语言模型）上添加一个适配器来进行微调，只有这个适配器是可训练的，而LLM的原始参数保持冻结。这种方法显著减少了需要训练的参数数量，从而大大缩小了优化器的状态。因此，与标准的完全微调相比，LoRA微调消耗的内存大大减少。

然而，根据LoRA的超参数，如秩和目标模块的数量，LoRA可能仍会创建具有数亿个参数的非常大的适配器，这些适配器太大，无法在消费者硬件上进行微调。

已经提出了许多替代方案来减少适配器的大小。

在本文中，我回顾了VeRA，这是一种将适配器缩小100倍的LoRA替代方案。我使用VeRA对Llama 3进行了微调，并将其性能与LoRA进行了比较。

# VeRA：基于随机矩阵的向量微调

VeRA在本文中提出：

[VeRA：基于随机矩阵的向量适配](https://arxiv.org/abs/2310.11454)

*注意：这是我最喜欢的论文之一，展示了LoRA的替代方案。它写得非常好*…
