# 大型语言模型，GPT-1 — 生成式预训练Transformer

> 原文：[https://towardsdatascience.com/large-language-models-gpt-1-generative-pre-trained-transformer-7b895f296d3b?source=collection_archive---------0-----------------------#2024-01-27](https://towardsdatascience.com/large-language-models-gpt-1-generative-pre-trained-transformer-7b895f296d3b?source=collection_archive---------0-----------------------#2024-01-27)

## 深入探讨第一个版本的巨大GPT模型的工作结构

[](https://medium.com/@slavahead?source=post_page---byline--7b895f296d3b--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--7b895f296d3b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7b895f296d3b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7b895f296d3b--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--7b895f296d3b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7b895f296d3b--------------------------------) ·10分钟阅读·2024年1月27日

--

![](../Images/693f9e9a838bc5b59082bdb32087f5b2.png)

# 介绍

2017年是机器学习历史上具有里程碑意义的一年。来自Google Brain团队的研究人员提出了**Transformer**，它迅速超越了大多数现有的深度学习方法。著名的**注意力**机制成为未来基于Transformer的模型中的关键组成部分。Transformer架构的一个令人惊讶的事实是它的巨大灵活性：它可以高效地用于多种机器学习任务类型，包括自然语言处理、图像和视频处理问题。

原始的Transformer可以分解为两个部分，分别称为**编码器**和**解码器**。顾名思义，编码器的目标是将输入序列编码为一个数字向量——这是机器可以理解的低级格式。另一方面，解码器接受编码后的序列，并通过应用语言建模任务，生成一个新的序列。

编码器和解码器可以单独用于特定任务。两种最著名的模型，它们的部分组件来自原始的Transformer，分别叫做[**BERT**](https://medium.com/towards-data-science/bert-3d1bf880386a)（来自Transformer的双向编码表示），包含…
