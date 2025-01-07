# Mistral-NeMo: 通过量化Minitron减少4.1倍大小

> 原文：[https://towardsdatascience.com/mistral-nemo-4-1x-smaller-with-quantized-minitron-9d6ad7b70981?source=collection_archive---------7-----------------------#2024-08-29](https://towardsdatascience.com/mistral-nemo-4-1x-smaller-with-quantized-minitron-9d6ad7b70981?source=collection_archive---------7-----------------------#2024-08-29)

## 剪枝、知识蒸馏和4位量化如何使先进的AI模型变得更加易于访问和具备成本效益

[](https://medium.com/@bnjmn_marie?source=post_page---byline--9d6ad7b70981--------------------------------)[![本杰明·马里](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--9d6ad7b70981--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9d6ad7b70981--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9d6ad7b70981--------------------------------) [本杰明·马里](https://medium.com/@bnjmn_marie?source=post_page---byline--9d6ad7b70981--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d6ad7b70981--------------------------------) ·9分钟阅读·2024年8月29日

--

![](../Images/adf13515087f8d860ba67eab0cb2f9c9.png)

作者提供的图片 — 制作自[Pixabay](https://pixabay.com/vectors/llama-alpaca-animal-mammal-zoo-297668/)的插图

NVIDIA的Minitron通过剪枝最不重要的权重来压缩大型语言模型（LLMs），然后通过知识蒸馏进行重新训练。这种方法显著减少了模型的大小，同时保持了其准确性。

[NVIDIA发布了Llama 3.1和Mistral-NeMo的Minitron版本](https://developer.nvidia.com/blog/mistral-nemo-minitron-8b-foundation-model-delivers-unparalleled-accuracy/?ncid=ref-inor-263670%2F)，分别将其参数数量从8B减少到4B，以及从12B减少到8B。

*为什么这很重要？*

虽然Mistral-NeMo无法在消费级GPU上运行，但其Minitron版本可以。一个24 GB的GPU就足够了。然而，这也可以通过对Mistral-NeMo进行量化来实现。4位量化方法现在已经足够准确。

*但是，如果我们也能对Minitron模型进行量化呢？对于一个经过Minitron剪枝的模型，量化是否仍然足够准确？*

例如，Mistral-NeMo-Minitron的4位版本可以在8 GB的GPU上运行，显著降低推理成本。

在这篇文章中，我回顾了Minitron方法，探讨了如何通过剪枝和知识蒸馏来压缩大型语言模型（LLMs）。我们将…
