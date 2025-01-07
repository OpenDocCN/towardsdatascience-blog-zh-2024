# 门控循环单元（GRU）—— 改进的RNN

> 原文：[https://towardsdatascience.com/gated-recurrent-units-gru-improving-rnns-4467b66c5150?source=collection_archive---------7-----------------------#2024-06-15](https://towardsdatascience.com/gated-recurrent-units-gru-improving-rnns-4467b66c5150?source=collection_archive---------7-----------------------#2024-06-15)

## 解释门控循环神经网络（GRU）是如何工作的

[](https://medium.com/@egorhowell?source=post_page---byline--4467b66c5150--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--4467b66c5150--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4467b66c5150--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4467b66c5150--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--4467b66c5150--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4467b66c5150--------------------------------) ·10分钟阅读·2024年6月15日

--

![](../Images/c751c90c4f7c68dff3f32198562febf8.png)

"[https://www.flaticon.com/free-icons/neural-network](https://www.flaticon.com/free-icons/neural-network)" title="神经网络图标">神经网络图标由juicy_fish创建 — Flaticon。

在本文中，我将探讨循环神经网络（RNNs）的标准实现方式：门控循环单元（GRUs）。

GRU于2014年由[Kyunghyun Cho等人](https://arxiv.org/abs/1406.1078)提出，是对传统RNN的改进，因为它们较少受到梯度消失问题的影响，从而拥有更长的记忆能力。

它们与[长短期记忆（LSTM）](/long-short-term-memory-lstm-improving-rnns-40323d1c05f8?sk=03c1ce36eba874e0e0bc0d0267eb5314)网络类似，但操作较少，使它们在内存使用上更加高效。

我们将涵盖的内容：

+   RNN概述

+   梯度消失与梯度爆炸问题

+   LSTM概述

+   GRU是如何工作的

# 循环神经网络

循环神经网络是一种特别擅长处理基于序列的数据类型的神经网络，例如自然语言和时间序列数据。

它们通过添加一个“循环”神经元来实现这一点，允许信息从过去的输入和输出传递到下一步。
