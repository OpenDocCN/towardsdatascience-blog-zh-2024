# 批量归一化背后的数学

> 原文：[https://towardsdatascience.com/the-math-behind-batch-normalization-90ebbc0b1b0b?source=collection_archive---------2-----------------------#2024-05-08](https://towardsdatascience.com/the-math-behind-batch-normalization-90ebbc0b1b0b?source=collection_archive---------2-----------------------#2024-05-08)

## 探索批量归一化，这是神经网络的基石，理解其数学原理，并从零开始实现它。

[](https://medium.com/@cristianleo120?source=post_page---byline--90ebbc0b1b0b--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--90ebbc0b1b0b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--90ebbc0b1b0b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--90ebbc0b1b0b--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--90ebbc0b1b0b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--90ebbc0b1b0b--------------------------------) ·阅读时间 21 分钟·2024年5月8日

--

![](../Images/1702131733bebaab170620d4d519738f.png)

图像由 DALL-E 生成

批量归一化（Batch Normalization）是神经网络中的一个关键技术，它通过标准化每一层的输入来优化网络训练。它解决了内部协方差偏移（internal covariate shift）问题，即每一层的输入分布在训练过程中发生变化，导致学习过程复杂且效率低下。通过归一化这些输入，批量归一化帮助网络训练得更快且更稳定。此方法对于确保不同网络架构和任务（包括图像识别和自然语言处理）中的可靠性能至关重要。在本文中，我们将深入探讨批量归一化的原理和数学基础，并展示如何从零开始在 Python 中实现它。

**索引**

[**1: 介绍**](#4495)

[**2: 归一化的必要性**](#aae3)

[**3: 数学与机制**](#477c)

[∘ 3.1: 克服协方差偏移](https://medium.com/p/90ebbc0b1b0b/edit#cd9f)

∘ [3.2: 缩放和平移步骤](#312b)

∘ [3.3: 批量归一化的流](#b097)

∘ [3.4: 激活分布](#e2d9)

[**4: 从零开始在 Python 中应用**](#df72)

∘ [4.1: 从零开始的批量归一化](#2add)…
