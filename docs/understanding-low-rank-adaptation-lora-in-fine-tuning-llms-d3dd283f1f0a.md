# 理解低秩适应（LoRA）在微调大规模语言模型中的应用

> 原文：[`towardsdatascience.com/understanding-low-rank-adaptation-lora-in-fine-tuning-llms-d3dd283f1f0a?source=collection_archive---------3-----------------------#2024-05-24`](https://towardsdatascience.com/understanding-low-rank-adaptation-lora-in-fine-tuning-llms-d3dd283f1f0a?source=collection_archive---------3-----------------------#2024-05-24)

## 本文将详细讲解 LoRA 如何用于微调大规模语言模型，遵循[“LoRA：大规模语言模型的低秩适应”](https://arxiv.org/pdf/2106.09685)论文中提出的方法。

[](https://medium.com/@mgunton7?source=post_page---byline--d3dd283f1f0a--------------------------------)![Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--d3dd283f1f0a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d3dd283f1f0a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3dd283f1f0a--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--d3dd283f1f0a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3dd283f1f0a--------------------------------) ·阅读时长：6 分钟·2024 年 5 月 24 日

--

![](img/fcdf35f66c36e62f303d26f31190e70b.png)

图片由作者提供 — 由 Stable Diffusion 2.1 生成

微调可能是大规模语言模型（LLMs）中最常被讨论的技术方面之一。大多数人都知道训练这些模型是昂贵的，并且需要大量的资金投入，因此看到通过在已有模型上进行微调，结合自己的数据创建出一个具有一定独特性的模型，是令人兴奋的。

有多种方法可以对模型进行微调，但目前最受欢迎的方式之一是 LoRA 方法（低秩适应，Low Rank Adaptation），它在[“LoRA：大规模语言模型的低秩适应”](https://arxiv.org/pdf/2106.09685)论文中有详细讨论。

在深入探讨 LoRA 的工作原理之前，我们需要了解一些矩阵背景知识以及微调机器学习模型的一些基本概念。

# 矩阵背景术语

几乎所有的机器学习模型都会将其权重存储为矩阵。因此，理解一些线性代数的基本知识有助于直观理解...
