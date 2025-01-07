# Marlin：接近理想的 4 位大规模语言模型推理速度

> 原文：[`towardsdatascience.com/marlin-nearly-ideal-inference-speed-for-4-bit-large-language-models-feb0b610dd8e?source=collection_archive---------3-----------------------#2024-03-30`](https://towardsdatascience.com/marlin-nearly-ideal-inference-speed-for-4-bit-large-language-models-feb0b610dd8e?source=collection_archive---------3-----------------------#2024-03-30)

## 比使用 fp16 参数的推理速度快多达 4 倍

[](https://medium.com/@bnjmn_marie?source=post_page---byline--feb0b610dd8e--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--feb0b610dd8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--feb0b610dd8e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--feb0b610dd8e--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--feb0b610dd8e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--feb0b610dd8e--------------------------------) ·阅读时间：6 分钟·2024 年 3 月 30 日

--

![](img/c9dfbfd469dab2a651a7a522eb2d344c.png)

使用 DALL-E 生成

大规模语言模型（LLM）通常过于庞大，无法直接在消费级硬件上使用。为减少它们的大小，已经提出了多种技术来对 LLM 进行量化并降低其内存消耗。尽管最近关于 4 位量化的算法通常会发布自己的优化 CUDA 内核，但量化后的 LLM 的推理吞吐量仍远未达到理想水平。

使用 4 位模型进行推理，例如使用 INT4 数据类型，涉及到 INT4xFP16 操作，即使在现代 GPU 上也很慢，因此需要优化的 CUDA 内核。

奥地利科技与研究学院（ISTA）提出了**M**ixed **A**uto-**R**egressive **Lin**ear 内核（Marlin），这是一种极为优化的 INT4xFP16 矩阵乘法内核，可以提供接近理想（4 倍）的推理速度。

在本文中，我将解释 Marlin 如何实现这种加速。然后，我们将看到如何将现有的 GPTQ 模型转换为 Marlin 格式。我使用 Mistral 7B 进行演示，并通过 vLLM 检查推理速度。

# Marlin：最大化 INT4 LLM 的 GPU 使用率
