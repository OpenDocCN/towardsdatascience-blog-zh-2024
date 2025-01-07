# 深度学习图解，第 5 部分：长短期记忆（LSTM）

> 原文：[https://towardsdatascience.com/deep-learning-illustrated-part-5-long-short-term-memory-lstm-d379fbbc9bc6?source=collection_archive---------2-----------------------#2024-06-21](https://towardsdatascience.com/deep-learning-illustrated-part-5-long-short-term-memory-lstm-d379fbbc9bc6?source=collection_archive---------2-----------------------#2024-06-21)

## 关于 LSTM 内部工作原理的图解和直观指南

[](https://medium.com/@shreya.rao?source=post_page---byline--d379fbbc9bc6--------------------------------)[![Shreya Rao](../Images/03f13be6f5f67783d32f0798f09a4f86.png)](https://medium.com/@shreya.rao?source=post_page---byline--d379fbbc9bc6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d379fbbc9bc6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d379fbbc9bc6--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--d379fbbc9bc6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d379fbbc9bc6--------------------------------) ·9 分钟阅读·2024 年 6 月 21 日

--

欢迎来到我们通过深度学习的图解旅程的第 5 部分！

![Shreya Rao](../Images/45d3d481fab74a720c78346bc47e95fd.png)

[Shreya Rao](https://medium.com/@shreya.rao?source=post_page-----d379fbbc9bc6--------------------------------)

## 深度学习，图解

[查看列表](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640?source=post_page-----d379fbbc9bc6--------------------------------)5 篇故事！[](../Images/9668eeb3fd221bb26c2341a0ec0bfeab.png)![](../Images/1c261ce54b80b877b7737964ba5bf3f2.png)![](../Images/10364c8fdf64c9c6fb8300ce74259d00.png)

今天我们要讲解长短期记忆（LSTM）网络，它是对我们在[上一篇文章](https://medium.com/towards-data-science/deep-learning-illustrated-part-4-recurrent-neural-networks-d0121f27bc74)中讨论的常规递归神经网络（RNN）的升级版。我们看到，RNN 被用来解决基于序列的问题，但在长距离信息保留方面存在困难，导致短期记忆问题。此时，LSTM 就能挺身而出，拯救局面。它们使用与 RNN 相同的递归结构，但进行了改进。那么，让我们看看它们是如何实现这一点的。

*旁注——这是我写过的最喜欢的文章之一，所以我迫不及待想带你们一起踏上这段旅程！*

首先，让我们回顾一下之前我们在 RNN 中所做的事情。我们有一个神经网络，输入为 *x*，一个隐藏层，包含一个带有 tanh 激活函数的神经元，以及一个带有 sigmoid 激活函数的输出神经元。因此，RNN 的第一步看起来大概是这样的：
