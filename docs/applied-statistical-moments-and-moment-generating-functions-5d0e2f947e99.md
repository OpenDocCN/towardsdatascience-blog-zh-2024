# 应用统计矩和矩生成函数

> 原文：[https://towardsdatascience.com/applied-statistical-moments-and-moment-generating-functions-5d0e2f947e99?source=collection_archive---------11-----------------------#2024-01-22](https://towardsdatascience.com/applied-statistical-moments-and-moment-generating-functions-5d0e2f947e99?source=collection_archive---------11-----------------------#2024-01-22)

## 动机、定义及应用

[](https://romanmichaelpaolucci.medium.com/?source=post_page---byline--5d0e2f947e99--------------------------------)[![Roman Paolucci](../Images/d0072d982b6327bdeffa615044a765ad.png)](https://romanmichaelpaolucci.medium.com/?source=post_page---byline--5d0e2f947e99--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5d0e2f947e99--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5d0e2f947e99--------------------------------) [Roman Paolucci](https://romanmichaelpaolucci.medium.com/?source=post_page---byline--5d0e2f947e99--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5d0e2f947e99--------------------------------) ·9分钟阅读·2024年1月22日

--

![](../Images/e430936579ded789bbbb2f1739e3e934.png)

图片由[Milad Fakurian](https://unsplash.com/@fakurian?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

介绍性和基础的概率与统计课程通常会在随机变量的上下文中讨论期望和方差。少数课程甚至会讨论更高阶的矩，如偏度和峰度，并结合*矩生成函数*的概念。

如果你曾参加过我的本科概率与统计课程，你~可能~和我当时一样感到困惑。我们中的许多人不明白为什么我们不能像计算前两个矩（期望和方差）那样，直接计算更高阶的矩。

现在我了解得更多了，我会说：“孩子们，试着解决积分题吧”。在本文中，我清楚地阐述了在正态随机变量的背景下，矩生成函数的动机和定义，以及标准布朗运动的矩。

本文分为以下几个部分。

+   **期望、方差与统计矩的定义**

+   **矩生成函数与正态随机变量**

+   **通过矩生成函数推导矩**

## **期望、方差与更高阶矩的定义**
