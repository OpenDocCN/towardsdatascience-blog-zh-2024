# 深度学习图解，第三部分：卷积神经网络

> 原文：[`towardsdatascience.com/deep-learning-illustrated-part-3-convolutional-neural-networks-96b900b0b9e0?source=collection_archive---------3-----------------------#2024-05-11`](https://towardsdatascience.com/deep-learning-illustrated-part-3-convolutional-neural-networks-96b900b0b9e0?source=collection_archive---------3-----------------------#2024-05-11)

## 一份关于卷积神经网络（CNN）内部工作原理的图解和直观指南

[](https://medium.com/@shreya.rao?source=post_page---byline--96b900b0b9e0--------------------------------)![Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--96b900b0b9e0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--96b900b0b9e0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--96b900b0b9e0--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--96b900b0b9e0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--96b900b0b9e0--------------------------------) ·阅读时间 15 分钟·2024 年 5 月 11 日

--

欢迎来到我们图解深度学习之旅的第三部分。如果你错过了之前的文章，一定要回去阅读，它们为我们今天要深入探讨的内容打下了基础。

![Shreya Rao](img/45d3d481fab74a720c78346bc47e95fd.png)

[Shreya Rao](https://medium.com/@shreya.rao?source=post_page-----96b900b0b9e0--------------------------------)

## 深度学习图解

[查看列表](https://medium.com/@shreya.rao/list/deep-learning-illustrated-ae6c27de1640?source=post_page-----96b900b0b9e0--------------------------------)5 篇故事！[](../Images/9668eeb3fd221bb26c2341a0ec0bfeab.png)![](img/1c261ce54b80b877b7737964ba5bf3f2.png)![](img/10364c8fdf64c9c6fb8300ce74259d00.png)

为了快速回顾，我们之前通过构建一个[简单模型来预测冰淇淋店的每日收入](https://medium.com/towards-data-science/deep-learning-illustrated-part-2-how-does-a-neural-network-learn-481f70c1b474)来讨论神经网络的内部工作原理。我们发现，神经网络通过利用多个神经元的结合力量，能够处理复杂问题。这使得它们能够发现数据中可能难以识别的模式。我们还了解到，神经网络主要解决两种类型的问题：回归和分类。

就像我们构建了一个收入预测模型一样，通过修改结构，我们可以创建模型来解决各种问题。卷积神经网络（CNN）是专为图像识别任务设计的模型。然而，它们依然依赖于我们迄今为止遇到的模型的相同基本原理（再加上几个额外的步骤）。今天，我们将探索卷积神经网络的内部工作原理，并准确了解它到底是怎样的……
