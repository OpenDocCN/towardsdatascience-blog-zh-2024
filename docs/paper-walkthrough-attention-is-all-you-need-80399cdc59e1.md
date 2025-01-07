# 论文解析：Attention Is All You Need

> 原文：[https://towardsdatascience.com/paper-walkthrough-attention-is-all-you-need-80399cdc59e1?source=collection_archive---------1-----------------------#2024-11-03](https://towardsdatascience.com/paper-walkthrough-attention-is-all-you-need-80399cdc59e1?source=collection_archive---------1-----------------------#2024-11-03)

## 完整的从零开始实现Transformer的指南

[](https://medium.com/@muhammad_ardi?source=post_page---byline--80399cdc59e1--------------------------------)[![Muhammad Ardi](../Images/b59b3752bc33df0166eea834bbdb122f.png)](https://medium.com/@muhammad_ardi?source=post_page---byline--80399cdc59e1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--80399cdc59e1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--80399cdc59e1--------------------------------) [Muhammad Ardi](https://medium.com/@muhammad_ardi?source=post_page---byline--80399cdc59e1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--80399cdc59e1--------------------------------) ·阅读时间42分钟·2024年11月3日

--

![](../Images/d18eed9a18d83ad060417f4fe0323909.png)

图片由[Samule Sun](https://unsplash.com/@samule?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

正如标题所示，本文将从零开始用PyTorch实现Transformer架构——没错，真的是从零开始。在开始之前，让我简要介绍一下该架构。Transformer首次出现在2017年Vaswani等人撰写的论文《*Attention Is All You Need*》中[1]。该神经网络模型旨在执行*seq2seq*（序列到序列）任务，它接收一个序列作为输入，并期望返回另一个序列作为输出，应用场景包括机器翻译和问答系统。

在Transformer出现之前，我们通常使用基于RNN的模型，如LSTM或GRU，来完成*seq2seq*任务。这些模型确实能够捕获上下文，但它们是按顺序处理的。这种方法使得捕捉长程依赖变得具有挑战性，尤其是当重要的上下文信息远在当前时间步之前时。相比之下，Transformer能够自由地关注序列中任何它认为重要的部分，而不受顺序处理的限制。

## Transformer组件
