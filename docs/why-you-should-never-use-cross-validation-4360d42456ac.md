# 为什么你永远不应该使用交叉验证

> 原文：[https://towardsdatascience.com/why-you-should-never-use-cross-validation-4360d42456ac?source=collection_archive---------0-----------------------#2024-03-27](https://towardsdatascience.com/why-you-should-never-use-cross-validation-4360d42456ac?source=collection_archive---------0-----------------------#2024-03-27)

## 在真实世界的应用中，使用随机交叉验证始终是一个错误的选择。以下是原因。

[](https://medium.com/@mazzanti.sam?source=post_page---byline--4360d42456ac--------------------------------)[![Samuele Mazzanti](../Images/432477d6418a3f79bf25dec42755d364.png)](https://medium.com/@mazzanti.sam?source=post_page---byline--4360d42456ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4360d42456ac--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4360d42456ac--------------------------------) [Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--4360d42456ac--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4360d42456ac--------------------------------) ·12分钟阅读·2024年3月27日

--

![](../Images/9859c7e698f3b6ef46b648a9c319e548.png)

[作者插图]

作为一名数据科学家，我经常需要快速粗略地估算一个预测模型在给定数据集上的表现。很长一段时间里，我是通过交叉验证来完成这一点的。然后，我意识到我完全走错了方向。事实上，

> 对于真实世界的问题，交叉验证完全不可信。

由于我敢打赌很多数据科学家仍然依赖这种技术，因此我认为深入探讨这个话题非常有意义。

在这篇文章中，我将通过一个玩具示例和一个真实数据集，讲解为什么交叉验证在处理真实世界问题时永远不是一个好选择。

# 什么是交叉验证？

交叉验证是一种模型验证技术，用于估算在一个数据集上训练的模型在新（未见过的）数据集上的表现。

> 注：交叉验证有很多种类型。在本文中，为了简单起见，**当我们说“交叉验证”时，我们指的是随机K折交叉验证**，这是迄今为止最常用的……
