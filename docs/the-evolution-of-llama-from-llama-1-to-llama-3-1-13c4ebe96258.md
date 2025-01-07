# Llama的发展：从Llama 1到Llama 3.1

> 原文：[https://towardsdatascience.com/the-evolution-of-llama-from-llama-1-to-llama-3-1-13c4ebe96258?source=collection_archive---------5-----------------------#2024-09-06](https://towardsdatascience.com/the-evolution-of-llama-from-llama-1-to-llama-3-1-13c4ebe96258?source=collection_archive---------5-----------------------#2024-09-06)

## 《Meta AI Llama模型家族的进展与创新全面指南》

[](https://medium.com/@luisroque?source=post_page---byline--13c4ebe96258--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--13c4ebe96258--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--13c4ebe96258--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--13c4ebe96258--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--13c4ebe96258--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--13c4ebe96258--------------------------------) ·15分钟阅读·2024年9月6日

--

*本文由Rafael Guedes共同撰写。*

# 引言

Meta发布了其大型语言模型（LLM）Llama的三个主要版本，并进行了一个小的更新（版本3.1）。Llama在2023年初的首次发布标志着自然语言处理（NLP）领域开源社区的一次重大进步。Meta通过分享其最新的LLM版本，持续为该社区做出贡献。

为了确保准确性，我们应该区分开放LLM和开源LLM。传统的开源软件会在特定的公共使用和修改许可下提供其源代码。在LLM的背景下，开放LLM通常会披露模型权重和初始代码。同时，开源LLM还会分享整个训练过程，包括训练数据，并采用宽松的许可。如今，大多数模型，包括Meta的Llama，都属于开放LLM类别，因为它们没有发布用于训练的数据集。

Llama经历了三次关键的架构迭代。版本1对原始的Transformer架构进行了若干增强。版本2在更大的模型中实现了Grouped-Query Attention（GQA）。版本3将GQA扩展到较小的模型，并引入了更高效的……
