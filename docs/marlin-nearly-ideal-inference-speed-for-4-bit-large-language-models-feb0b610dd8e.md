# Marlin：接近理想的4位大规模语言模型推理速度

> 原文：[https://towardsdatascience.com/marlin-nearly-ideal-inference-speed-for-4-bit-large-language-models-feb0b610dd8e?source=collection_archive---------3-----------------------#2024-03-30](https://towardsdatascience.com/marlin-nearly-ideal-inference-speed-for-4-bit-large-language-models-feb0b610dd8e?source=collection_archive---------3-----------------------#2024-03-30)

## 比使用fp16参数的推理速度快多达4倍

[](https://medium.com/@bnjmn_marie?source=post_page---byline--feb0b610dd8e--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--feb0b610dd8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--feb0b610dd8e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--feb0b610dd8e--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--feb0b610dd8e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--feb0b610dd8e--------------------------------) ·阅读时间：6分钟·2024年3月30日

--

![](../Images/c9dfbfd469dab2a651a7a522eb2d344c.png)

使用DALL-E生成

大规模语言模型（LLM）通常过于庞大，无法直接在消费级硬件上使用。为减少它们的大小，已经提出了多种技术来对LLM进行量化并降低其内存消耗。尽管最近关于4位量化的算法通常会发布自己的优化CUDA内核，但量化后的LLM的推理吞吐量仍远未达到理想水平。

使用4位模型进行推理，例如使用INT4数据类型，涉及到INT4xFP16操作，即使在现代GPU上也很慢，因此需要优化的CUDA内核。

奥地利科技与研究学院（ISTA）提出了**M**ixed **A**uto-**R**egressive **Lin**ear内核（Marlin），这是一种极为优化的INT4xFP16矩阵乘法内核，可以提供接近理想（4倍）的推理速度。

在本文中，我将解释Marlin如何实现这种加速。然后，我们将看到如何将现有的GPTQ模型转换为Marlin格式。我使用Mistral 7B进行演示，并通过vLLM检查推理速度。

# Marlin：最大化INT4 LLM的GPU使用率
