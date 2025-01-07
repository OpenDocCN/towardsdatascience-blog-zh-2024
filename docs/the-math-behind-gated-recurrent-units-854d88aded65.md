# 门控递归单元背后的数学原理

> 原文：[https://towardsdatascience.com/the-math-behind-gated-recurrent-units-854d88aded65?source=collection_archive---------5-----------------------#2024-06-04](https://towardsdatascience.com/the-math-behind-gated-recurrent-units-854d88aded65?source=collection_archive---------5-----------------------#2024-06-04)

## 深入探索门控递归单元（GRU）的高级深度学习，理解其数学原理，并从零开始实现它们。

[](https://medium.com/@cristianleo120?source=post_page---byline--854d88aded65--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--854d88aded65--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--854d88aded65--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--854d88aded65--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--854d88aded65--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--854d88aded65--------------------------------) ·27分钟阅读·2024年6月4日

--

![](../Images/a90e807ae5a64f36ab11ffa122fe4a0a.png)

由DALL-E生成的图像

门控递归单元（GRU）是一种强大的递归神经网络（RNN），旨在高效处理序列数据。本文将探讨GRU是什么，它的数学原理，并且我们将从零开始用Python实现一个GRU模型。

# 索引

[**1: 理解基础知识**](#6a1a)

∘ [1.1: 什么是GRU？](#919d)

∘ [1.2: 与LSTM和传统RNN的比较](#ffb2)

[**2: GRU的架构**](#9a12)

∘ [2.1: GRU单元结构](#f047)

[**3: GRU的数学原理**](#c218)

∘ [3.1: 更新门](#798d)

∘ [3.2: 重置门](#760d)

∘ [3.3: 候选激活](#adc2)

∘ [3.4: 逐步示例](#6c28)

[**4: 在Python中从零开始构建GRU**](#49b9)

∘ [4.1: 导入和自定义类](#3222)

∘ [4.2: GRU类](#4bd1)

∘ [4.3: GRUTrainer类](#71bd)

∘ [4.4: TimeSeriesDataset类](#3426)

∘ [4.5: 训练GRU模型](#d983)

[**5: 结论**](#790b)
