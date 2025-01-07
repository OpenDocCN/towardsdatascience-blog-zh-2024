# 论文解析：Attention Is All You Need

> 原文：[`towardsdatascience.com/paper-walkthrough-attention-is-all-you-need-80399cdc59e1?source=collection_archive---------1-----------------------#2024-11-03`](https://towardsdatascience.com/paper-walkthrough-attention-is-all-you-need-80399cdc59e1?source=collection_archive---------1-----------------------#2024-11-03)

## 完整的从零开始实现 Transformer 的指南

[](https://medium.com/@muhammad_ardi?source=post_page---byline--80399cdc59e1--------------------------------)![Muhammad Ardi](https://medium.com/@muhammad_ardi?source=post_page---byline--80399cdc59e1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--80399cdc59e1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--80399cdc59e1--------------------------------) [Muhammad Ardi](https://medium.com/@muhammad_ardi?source=post_page---byline--80399cdc59e1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--80399cdc59e1--------------------------------) ·阅读时间 42 分钟·2024 年 11 月 3 日

--

![](img/d18eed9a18d83ad060417f4fe0323909.png)

图片由[Samule Sun](https://unsplash.com/@samule?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

正如标题所示，本文将从零开始用 PyTorch 实现 Transformer 架构——没错，真的是从零开始。在开始之前，让我简要介绍一下该架构。Transformer 首次出现在 2017 年 Vaswani 等人撰写的论文《*Attention Is All You Need*》中[1]。该神经网络模型旨在执行*seq2seq*（序列到序列）任务，它接收一个序列作为输入，并期望返回另一个序列作为输出，应用场景包括机器翻译和问答系统。

在 Transformer 出现之前，我们通常使用基于 RNN 的模型，如 LSTM 或 GRU，来完成*seq2seq*任务。这些模型确实能够捕获上下文，但它们是按顺序处理的。这种方法使得捕捉长程依赖变得具有挑战性，尤其是当重要的上下文信息远在当前时间步之前时。相比之下，Transformer 能够自由地关注序列中任何它认为重要的部分，而不受顺序处理的限制。

## Transformer 组件
