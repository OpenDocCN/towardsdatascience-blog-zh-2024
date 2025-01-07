# 门控递归单元背后的数学原理

> 原文：[`towardsdatascience.com/the-math-behind-gated-recurrent-units-854d88aded65?source=collection_archive---------5-----------------------#2024-06-04`](https://towardsdatascience.com/the-math-behind-gated-recurrent-units-854d88aded65?source=collection_archive---------5-----------------------#2024-06-04)

## 深入探索门控递归单元（GRU）的高级深度学习，理解其数学原理，并从零开始实现它们。

[](https://medium.com/@cristianleo120?source=post_page---byline--854d88aded65--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--854d88aded65--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--854d88aded65--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--854d88aded65--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--854d88aded65--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--854d88aded65--------------------------------) ·27 分钟阅读·2024 年 6 月 4 日

--

![](img/a90e807ae5a64f36ab11ffa122fe4a0a.png)

由 DALL-E 生成的图像

门控递归单元（GRU）是一种强大的递归神经网络（RNN），旨在高效处理序列数据。本文将探讨 GRU 是什么，它的数学原理，并且我们将从零开始用 Python 实现一个 GRU 模型。

# 索引

**1: 理解基础知识**

∘ 1.1: 什么是 GRU？

∘ 1.2: 与 LSTM 和传统 RNN 的比较

**2: GRU 的架构**

∘ 2.1: GRU 单元结构

**3: GRU 的数学原理**

∘ 3.1: 更新门

∘ 3.2: 重置门

∘ 3.3: 候选激活

∘ 3.4: 逐步示例

**4: 在 Python 中从零开始构建 GRU**

∘ 4.1: 导入和自定义类

∘ 4.2: GRU 类

∘ 4.3: GRUTrainer 类

∘ 4.4: TimeSeriesDataset 类

∘ 4.5: 训练 GRU 模型

**5: 结论**
