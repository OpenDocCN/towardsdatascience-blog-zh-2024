# LSTM背后的数学

> 原文：[https://towardsdatascience.com/the-math-behind-lstm-9069b835289d?source=collection_archive---------4-----------------------#2024-05-01](https://towardsdatascience.com/the-math-behind-lstm-9069b835289d?source=collection_archive---------4-----------------------#2024-05-01)

## 深入了解LSTM，理解其数学原理，并从零开始实现它们

[](https://medium.com/@cristianleo120?source=post_page---byline--9069b835289d--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--9069b835289d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9069b835289d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9069b835289d--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--9069b835289d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9069b835289d--------------------------------) ·阅读时间25分钟·2024年5月1日

--

![](../Images/40ce1511d5d70098a51d624ac0316c41.png)

图像由DALL-E生成

**索引**

[**1: 介绍**](#41e5)

[**2: LSTM的架构**](#6b2f)

∘ [2.1: LSTM门控](#f510)

∘ [2.2: 信息流管理](#6fc4)

[**3: LSTM背后的数学**](#ae46)

∘ [3.1\. LSTM单元中的数学](#a8be)

∘ [3.2: 门控机制](#eba4)

∘ [3.3: 与基本RNN单元操作的对比](#bc48)

[**4: 使用Python从零构建LSTM**](#42fc)

∘ [4.1: 导入和初步设置](#074b)

∘ [4:2: LSTM类](#d45c)

∘ [4.3: 训练与验证](#9cb3)

∘ [4.4: 数据预处理](#fe87)

∘ [4.5: 模型训练](#1ac1)

[**5: 高级LSTM模型**](#218e)

∘ [5.1: 双向LSTM](#d622)

∘ [5.2: 堆叠LSTM](#c75d)

∘ [5.3: 窥视孔连接](#dd62)

[**6: 结论**](#39a1)

[参考文献](#2068)

# 1: 介绍

在我们上次讨论循环神经网络（RNN）时，我们探讨了它们的设计如何使得它们能够有效地处理序列。这使得它们非常适合处理...
