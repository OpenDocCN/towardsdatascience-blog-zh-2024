# 深入了解支持向量机（SVM）在线性和非线性分类与回归中的应用

> 原文：[https://towardsdatascience.com/in-depth-support-vector-machines-svms-for-linear-non-linear-classification-regression-2f743962bfee?source=collection_archive---------6-----------------------#2024-05-19](https://towardsdatascience.com/in-depth-support-vector-machines-svms-for-linear-non-linear-classification-regression-2f743962bfee?source=collection_archive---------6-----------------------#2024-05-19)

## 深入理解SVM在背后是如何工作的

[](https://rukshanpramoditha.medium.com/?source=post_page---byline--2f743962bfee--------------------------------)[![Rukshan Pramoditha](../Images/b80426aff64ff186cb915795644590b1.png)](https://rukshanpramoditha.medium.com/?source=post_page---byline--2f743962bfee--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2f743962bfee--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2f743962bfee--------------------------------) [Rukshan Pramoditha](https://rukshanpramoditha.medium.com/?source=post_page---byline--2f743962bfee--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2f743962bfee--------------------------------) ·10分钟阅读·2024年5月19日

--

![](../Images/698226d169de8e867baa408fdb7fbf47.png)

照片来自[vackground.com](https://unsplash.com/@vackground?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/a-close-up-of-a-blue-and-orange-abstract-background-jEDNGHrrVRw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

支持向量机是一种多用途的机器学习算法，主要用于线性和非线性分类，也可以用于线性和非线性回归。

它属于监督学习类别，需要特征矩阵和标签向量来训练模型。

支持向量机的主要目标是找到一个超平面，将数据点以最佳方式分隔成两个或多个组（类别）。

# 不同维度中超平面的定义

在机器学习中，超平面是一个*线性决策边界*，其维度比数据的维度少1。如果数据被绘制在一个N维空间中，超平面具有N-1维。

## **示例：**

+   在一维（1D）空间（直线）中，超平面是一个点（0D）。

+   在二维（2D）空间中，超平面是一个直线（1D）。
