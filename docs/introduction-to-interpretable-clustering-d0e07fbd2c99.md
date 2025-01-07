# 可解释聚类简介

> 原文：[`towardsdatascience.com/introduction-to-interpretable-clustering-d0e07fbd2c99?source=collection_archive---------3-----------------------#2024-08-01`](https://towardsdatascience.com/introduction-to-interpretable-clustering-d0e07fbd2c99?source=collection_archive---------3-----------------------#2024-08-01)

![](img/f00f83f4fd90e7f7f5816b140764a195.png)

图片由[Sergei A](https://unsplash.com/@sakulich?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 什么是可解释的聚类，为什么它很重要？

[](https://medium.com/@upadhyan?source=post_page---byline--d0e07fbd2c99--------------------------------)![Nakul Upadhya](https://medium.com/@upadhyan?source=post_page---byline--d0e07fbd2c99--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0e07fbd2c99--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0e07fbd2c99--------------------------------) [Nakul Upadhya](https://medium.com/@upadhyan?source=post_page---byline--d0e07fbd2c99--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0e07fbd2c99--------------------------------) ·阅读时间 11 分钟·2024 年 8 月 1 日

--

聚类是一个流行的无监督学习任务，用于将相似的数据点分组。尽管这是一个常见的机器学习任务，但大多数聚类算法并没有解释每个聚类的特征或为什么某个点会归类于某个聚类，这需要用户进行大量的聚类分析。随着数据集的增大，这一费时的过程变得异常困难。很讽刺的是，聚类的主要用途之一就是发现数据中的趋势和模式。

考虑到这些因素，难道不希望有一种方法不仅能够聚类数据，还能*同时*提供每个聚类的固有特征吗？这就是*可解释聚类*领域的作用所在。这些方法构建了一个模型，将数据点映射到聚类上，理想情况下，用户可以分析这个模型，找出每个聚类的特征。在本文中，我将讨论为什么这个领域很重要，并介绍一些可解释聚类的主要方向。

如果你对可解释的机器学习以及其他伦理人工智能方面感兴趣，可以考虑查看我的其他文章并关注我！
