# 长短期记忆（LSTM）—— 改进 RNNs

> 原文：[https://towardsdatascience.com/long-short-term-memory-lstm-improving-rnns-40323d1c05f8?source=collection_archive---------5-----------------------#2024-05-31](https://towardsdatascience.com/long-short-term-memory-lstm-improving-rnns-40323d1c05f8?source=collection_archive---------5-----------------------#2024-05-31)

## 最先进的 RNNs 如何工作

[](https://medium.com/@egorhowell?source=post_page---byline--40323d1c05f8--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--40323d1c05f8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40323d1c05f8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--40323d1c05f8--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--40323d1c05f8--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40323d1c05f8--------------------------------) ·阅读时长 9 分钟 ·2024年5月31日

--

![](../Images/f6f8a3bcbebfcdf3bcb18428e3166a5d.png)

"[https://www.flaticon.com/free-icons/neural-network](https://www.flaticon.com/free-icons/neural-network)" 标题为“neural network icons”的图标由 Freepik 创建 — Flaticon。

在本文中，我们将介绍长短期记忆网络（LSTMs），它们是常规的 vanilla 循环神经网络（RNNs）的变种，在处理长期依赖问题上表现更好。

它们使用不同的“门”来记住或忘记它们认为对预测有用或不重要的信息。

LSTMs 是 RNNs 的最先进版本。它们在工业界被广泛使用，并构成了我们今天所见的所有大型语言模型（LLMs）的基础。

我们将首先回顾 RNNs，解释梯度消失和梯度爆炸问题，然后深入探讨 LSTMs 如何工作以及为何它们是更好的模型。

# RNN 概述

循环神经网络（RNN）是常规前馈神经网络的变种，它们能够更好地处理自然语言和时间序列等序列数据。

它们通过拥有一个隐藏的循环神经元，将前一个输入和输出传递到下一层，从而实现这一点。下面是一个例子。
