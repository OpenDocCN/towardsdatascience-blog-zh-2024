# 离群点更难预测吗？

> 原文：[https://towardsdatascience.com/are-outliers-harder-to-predict-ae84dd4be465?source=collection_archive---------3-----------------------#2024-02-04](https://towardsdatascience.com/are-outliers-harder-to-predict-ae84dd4be465?source=collection_archive---------3-----------------------#2024-02-04)

## 一项关于机器学习模型在预测离群点时是否更容易出错的实证分析

[](https://medium.com/@mazzanti.sam?source=post_page---byline--ae84dd4be465--------------------------------)[![Samuele Mazzanti](../Images/432477d6418a3f79bf25dec42755d364.png)](https://medium.com/@mazzanti.sam?source=post_page---byline--ae84dd4be465--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ae84dd4be465--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ae84dd4be465--------------------------------) [Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--ae84dd4be465--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ae84dd4be465--------------------------------) ·阅读时间：8分钟·2024年2月4日

--

![](../Images/38fef5ef56cad68af3fda61fd41732be.png)

[图像由作者提供]

离群点是与大多数数据群体差异很大的个体。传统上，在实践者中，离群点往往不被信任，这也是为什么通常会采取删除离群点的措施来处理数据集。

然而，在处理真实数据时，离群点是常见的现象。**有时候，它们比其他数据点还要重要！** 例如，假设有一些离群点是因为他们是非常高付费的客户：你肯定不想丢弃他们，实际上，你可能希望特别关注他们。

离群点的一个有趣的——且未被充分探索的——方面是它们与机器学习模型的互动。我的感觉是，数据科学家们认为离群点会损害他们模型的表现。但这种看法很可能是基于一种先入为主的观念，而非真实的证据。

因此，我在本文中将尝试回答以下问题：

> 机器学习模型在预测离群点时是否更容易出错？

# 问题框架
