# 从零开始实现简单神经网络的反向传播

> 原文：[https://towardsdatascience.com/implementing-simple-neural-network-backpropagation-from-scratch-baba8a92d232?source=collection_archive---------6-----------------------#2024-03-20](https://towardsdatascience.com/implementing-simple-neural-network-backpropagation-from-scratch-baba8a92d232?source=collection_archive---------6-----------------------#2024-03-20)

## 解决XOR门问题——仅使用NumPy实现，并与PyTorch实现进行比较。

[](https://medium.com/@siqiyuanyuan?source=post_page---byline--baba8a92d232--------------------------------)[![Siqi Sun](../Images/04a48a3ebe3aef19f39a56cf6b2d9be7.png)](https://medium.com/@siqiyuanyuan?source=post_page---byline--baba8a92d232--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--baba8a92d232--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--baba8a92d232--------------------------------) [Siqi Sun](https://medium.com/@siqiyuanyuan?source=post_page---byline--baba8a92d232--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--baba8a92d232--------------------------------) ·9分钟阅读·2024年3月20日

--

## 我写这篇文章的原因：

我一直在使用机器学习库，但最近意识到自己并没有完全探索反向传播是如何工作的。

> **理解基础原理对于跟上最新技术发展并在机器学习项目中识别错误至关重要。**

在本文中，我分享了从零开始使用NumPy构建简单神经网络的经验，并将其与PyTorch实现进行性能对比。这将帮助你实际理解反向传播背后的基本概念。

## 大纲

**・XOR门问题简介**

**・构建一个2层神经网络**

**・前向传播**

**・反向传播的链式法则**

**・使用NumPy的实现**

**・与PyTorch结果的比较**

**・总结**

**・参考文献**

![](../Images/421384442e7f72887b37b14232986309.png)

图片来源：[Google DeepMind](https://unsplash.com/@googledeepmind?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## **XOR门问题简介**
