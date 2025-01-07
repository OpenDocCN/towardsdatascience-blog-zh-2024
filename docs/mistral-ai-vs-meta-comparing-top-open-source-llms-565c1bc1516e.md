# Mistral AI 与 Meta：对比顶级开源 LLM

> 原文：[`towardsdatascience.com/mistral-ai-vs-meta-comparing-top-open-source-llms-565c1bc1516e?source=collection_archive---------1-----------------------#2024-01-23`](https://towardsdatascience.com/mistral-ai-vs-meta-comparing-top-open-source-llms-565c1bc1516e?source=collection_archive---------1-----------------------#2024-01-23)

## 这是对 Mistral 7B 与 Llama 2 7B、Mixtral 8x7B 与 Llama 2 70B 的比较。

[](https://medium.com/@luisroque?source=post_page---byline--565c1bc1516e--------------------------------)![Luís Roque](https://medium.com/@luisroque?source=post_page---byline--565c1bc1516e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--565c1bc1516e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--565c1bc1516e--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--565c1bc1516e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--565c1bc1516e--------------------------------) ·阅读时间：16 分钟·2024 年 1 月 23 日

--

*本文由 Rafael Guedes 和我共同撰写。*

# 介绍

在自然语言处理（NLP）的最新发展中，特别是在大型语言模型（LLMs）方面，重点是提升模型性能，这通常会导致模型规模的增大。可以预见的是，模型规模的扩大也增加了计算成本和推理延迟，带来了在实际应用场景中部署和使用 LLM 时的障碍。

Mistral AI 是一家总部位于巴黎的欧洲公司，他们一直在研究如何提高模型性能，同时减少部署 LLM 所需的计算资源，以便能在实际用例中应用。Mistral 7B 是他们开发的最小型 LLM，它将两个创新概念引入到传统的 Transformer 架构中，分别是 Group-Query Attention（GQA）和 Sliding Window Attention（SWA）。这些组件加速了推理速度，并减少了在解码过程中对内存的需求，从而实现更高的吞吐量，并能够处理更长的令牌序列，同时不牺牲生成的响应质量，相比于在基准数据集上的 Llama 2 7B 表现更佳。

Mistral 7B 并不是他们唯一开发的模型，他们还创建了 Mixtral 8x7B 来与更大规模的 LLM（如 Llama 2 70B）竞争。除了使用 GQA 和 SWA 外，这个版本还增加了第三个…
