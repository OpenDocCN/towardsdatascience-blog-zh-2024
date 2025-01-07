# 为什么“统计显著性”是毫无意义的

> 原文：[https://towardsdatascience.com/why-statistical-significance-is-pointless-a7644be30266?source=collection_archive---------0-----------------------#2024-12-01](https://towardsdatascience.com/why-statistical-significance-is-pointless-a7644be30266?source=collection_archive---------0-----------------------#2024-12-01)

## 这是一个更好的数据驱动决策框架

[](https://medium.com/@mazzanti.sam?source=post_page---byline--a7644be30266--------------------------------)[![Samuele Mazzanti](../Images/432477d6418a3f79bf25dec42755d364.png)](https://medium.com/@mazzanti.sam?source=post_page---byline--a7644be30266--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a7644be30266--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a7644be30266--------------------------------) [Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--a7644be30266--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a7644be30266--------------------------------) ·9分钟阅读·2024年12月1日

--

![](../Images/1d2a800b6973bde6d6421d04ec461387.png)

[图片来源：作者]

数据科学家从事的是决策工作。我们的工作聚焦于如何在不确定性下做出明智的选择。

然而，在量化这种不确定性时，我们常常依赖“统计显著性”这一概念——这一工具，充其量只能提供一种肤浅的理解。

在本文中，我们将探讨为什么“统计显著性”是有缺陷的：任意的阈值、虚假的确定性感以及未能解决现实世界中的权衡问题。

最重要的是，我们将学习如何超越显著与非显著的二元思维，采用一个以经济影响和风险管理为基础的决策框架。

# 1\. 从一个例子开始

假设我们刚刚进行了一个A/B测试，评估一个旨在提高用户在我们网站上停留时间的新功能——从而提高他们的消费。

控制组包含了5,000名用户，治疗组包括另外5,000名用户。这为我们提供了两个数组，分别命名为`treatment`和`control`，每个数组包含5,000个值，代表各自组内用户的消费情况。
