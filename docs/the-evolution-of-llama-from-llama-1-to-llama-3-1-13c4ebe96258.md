# Llama 的发展：从 Llama 1 到 Llama 3.1

> 原文：[`towardsdatascience.com/the-evolution-of-llama-from-llama-1-to-llama-3-1-13c4ebe96258?source=collection_archive---------5-----------------------#2024-09-06`](https://towardsdatascience.com/the-evolution-of-llama-from-llama-1-to-llama-3-1-13c4ebe96258?source=collection_archive---------5-----------------------#2024-09-06)

## 《Meta AI Llama 模型家族的进展与创新全面指南》

[](https://medium.com/@luisroque?source=post_page---byline--13c4ebe96258--------------------------------)![Luís Roque](https://medium.com/@luisroque?source=post_page---byline--13c4ebe96258--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--13c4ebe96258--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--13c4ebe96258--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--13c4ebe96258--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--13c4ebe96258--------------------------------) ·15 分钟阅读·2024 年 9 月 6 日

--

*本文由 Rafael Guedes 共同撰写。*

# 引言

Meta 发布了其大型语言模型（LLM）Llama 的三个主要版本，并进行了一个小的更新（版本 3.1）。Llama 在 2023 年初的首次发布标志着自然语言处理（NLP）领域开源社区的一次重大进步。Meta 通过分享其最新的 LLM 版本，持续为该社区做出贡献。

为了确保准确性，我们应该区分开放 LLM 和开源 LLM。传统的开源软件会在特定的公共使用和修改许可下提供其源代码。在 LLM 的背景下，开放 LLM 通常会披露模型权重和初始代码。同时，开源 LLM 还会分享整个训练过程，包括训练数据，并采用宽松的许可。如今，大多数模型，包括 Meta 的 Llama，都属于开放 LLM 类别，因为它们没有发布用于训练的数据集。

Llama 经历了三次关键的架构迭代。版本 1 对原始的 Transformer 架构进行了若干增强。版本 2 在更大的模型中实现了 Grouped-Query Attention（GQA）。版本 3 将 GQA 扩展到较小的模型，并引入了更高效的……
