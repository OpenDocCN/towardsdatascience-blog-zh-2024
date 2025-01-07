# 数据模型设计101：复合键与代理键

> 原文：[https://towardsdatascience.com/data-model-design-101-composite-vs-surrogate-keys-032d8e1defe1?source=collection_archive---------7-----------------------#2024-02-09](https://towardsdatascience.com/data-model-design-101-composite-vs-surrogate-keys-032d8e1defe1?source=collection_archive---------7-----------------------#2024-02-09)

## 何时知道在数据模型中使用哪种类型的键

[](https://madison-schott.medium.com/?source=post_page---byline--032d8e1defe1--------------------------------)[![Madison Schott](../Images/0b82d0dd48629641abb439cef23ebe04.png)](https://madison-schott.medium.com/?source=post_page---byline--032d8e1defe1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--032d8e1defe1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--032d8e1defe1--------------------------------) [Madison Schott](https://madison-schott.medium.com/?source=post_page---byline--032d8e1defe1--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--032d8e1defe1--------------------------------) ·阅读时长4分钟·2024年2月9日

--

![](../Images/397a0c7af89858dec3a702bfa17c10f5.png)

图片由[Jason D](https://unsplash.com/@jasondeblooisphotography?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最近，我一直在编写一个数据模型，用来表示我们业务的一个新部分。这些数据需要提出很多问题，因为它非常难以直观理解。

这个数据模型要求我将来自两个不同来源的相似但不同的数据集合并成一个数据集。每当你合并两个数据集时，考虑那个将作为新数据集主键的唯一键是至关重要的。

不幸的是，你不能假设每个数据集中的主键会延续到结果集中。这是因为这些键，如果是递增的整数，往往会在不同数据集之间重复。

然而，你可以创建一个新的键。

在本文中，我们将讨论在数据模型中创建唯一键的两种选项——代理键和复合键。这两者之间有什么区别？你应该在什么情况下使用一个而不是另一个？

# 代理键与复合键

**复合键由多个标识字段组成，这些字段共同构成唯一性。** 它们是由真实世界的值创建的，其含义是可以理解的……
