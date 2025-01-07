# Mistral AI与Meta：对比顶级开源LLM

> 原文：[https://towardsdatascience.com/mistral-ai-vs-meta-comparing-top-open-source-llms-565c1bc1516e?source=collection_archive---------1-----------------------#2024-01-23](https://towardsdatascience.com/mistral-ai-vs-meta-comparing-top-open-source-llms-565c1bc1516e?source=collection_archive---------1-----------------------#2024-01-23)

## 这是对Mistral 7B与Llama 2 7B、Mixtral 8x7B与Llama 2 70B的比较。

[](https://medium.com/@luisroque?source=post_page---byline--565c1bc1516e--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--565c1bc1516e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--565c1bc1516e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--565c1bc1516e--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--565c1bc1516e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--565c1bc1516e--------------------------------) ·阅读时间：16分钟·2024年1月23日

--

*本文由Rafael Guedes和我共同撰写。*

# 介绍

在自然语言处理（NLP）的最新发展中，特别是在大型语言模型（LLMs）方面，重点是提升模型性能，这通常会导致模型规模的增大。可以预见的是，模型规模的扩大也增加了计算成本和推理延迟，带来了在实际应用场景中部署和使用LLM时的障碍。

Mistral AI是一家总部位于巴黎的欧洲公司，他们一直在研究如何提高模型性能，同时减少部署LLM所需的计算资源，以便能在实际用例中应用。Mistral 7B是他们开发的最小型LLM，它将两个创新概念引入到传统的Transformer架构中，分别是Group-Query Attention（GQA）和Sliding Window Attention（SWA）。这些组件加速了推理速度，并减少了在解码过程中对内存的需求，从而实现更高的吞吐量，并能够处理更长的令牌序列，同时不牺牲生成的响应质量，相比于在基准数据集上的Llama 2 7B表现更佳。

Mistral 7B并不是他们唯一开发的模型，他们还创建了Mixtral 8x7B来与更大规模的LLM（如Llama 2 70B）竞争。除了使用GQA和SWA外，这个版本还增加了第三个…
