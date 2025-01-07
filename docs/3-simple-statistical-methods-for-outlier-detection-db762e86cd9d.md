# 3种简单的统计方法用于离群值检测

> 原文：[https://towardsdatascience.com/3-simple-statistical-methods-for-outlier-detection-db762e86cd9d?source=collection_archive---------2-----------------------#2024-06-18](https://towardsdatascience.com/3-simple-statistical-methods-for-outlier-detection-db762e86cd9d?source=collection_archive---------2-----------------------#2024-06-18)

## 如果有效，就保持简单

[](https://medium.com/@pelletierhaden?source=post_page---byline--db762e86cd9d--------------------------------)[![Haden Pelletier](../Images/8f73fc8222e783883c4ebcaee14513e0.png)](https://medium.com/@pelletierhaden?source=post_page---byline--db762e86cd9d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--db762e86cd9d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--db762e86cd9d--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--db762e86cd9d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--db762e86cd9d--------------------------------) ·阅读时长6分钟·2024年6月18日

--

![](../Images/ddd1e6187e73f6e490be2476bc2a8216.png)

离群值。图片由作者提供

正如我们所知，数据科学家的一项重要工作是清理和预处理数据。大部分工作都涉及到离群值的检测和去除。大的离群值、异常峰值和错误数据会严重影响机器学习模型的训练，因此处理离群值非常重要。

但数据科学家并不总是使用像孤立森林或局部离群因子这样的机器学习模型来识别离群值。我在数据科学职业生涯中学到的一件事是，**如果更简单的解决方案有效，就使用它**。

我想给你提供3个简单的统计方法，用于检测离群值，这些方法在大多数情况下效果很好。我还会展示如何在Python中实现这些方法。

# **1. Z分数**

Z分数，也叫*标准分数*，是最常见的离群值检测方法之一。它本质上表示的是*一个数据点距离均值有多少个标准差*。

给定数据集中某个数据点的z分数计算公式如下：
