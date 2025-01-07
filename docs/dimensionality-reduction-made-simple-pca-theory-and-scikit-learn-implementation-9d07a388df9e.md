# 降维简化：PCA 理论与 Scikit-Learn 实现

> 原文：[`towardsdatascience.com/dimensionality-reduction-made-simple-pca-theory-and-scikit-learn-implementation-9d07a388df9e?source=collection_archive---------6-----------------------#2024-02-07`](https://towardsdatascience.com/dimensionality-reduction-made-simple-pca-theory-and-scikit-learn-implementation-9d07a388df9e?source=collection_archive---------6-----------------------#2024-02-07)

## 驯服维度灾难！学习降维（PCA）并用 Python 和 Scikit-Learn 实现。

[](https://medium.com/@riccardo.andreoni?source=post_page---byline--9d07a388df9e--------------------------------)![Riccardo Andreoni](https://medium.com/@riccardo.andreoni?source=post_page---byline--9d07a388df9e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9d07a388df9e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d07a388df9e--------------------------------) [Riccardo Andreoni](https://medium.com/@riccardo.andreoni?source=post_page---byline--9d07a388df9e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d07a388df9e--------------------------------) ·阅读时长：11 分钟·2024 年 2 月 7 日

--

![](img/dd97fa20ef6b63759949e85573f2a094.png)

图片来源：[unsplash.com](https://unsplash.com/photos/3DkouQeZjp4).

在小说[《平面国》](https://en.wikipedia.org/wiki/Flatland)中，生活在二维世界的角色在遇到三维生物时感到困惑，无法理解它的存在。我用这个类比来说明，在处理涉及成千上万甚至百万维度（即特征）的问题时，**惊人的现象会发生**，这些现象对我们的机器学习模型产生**灾难性的影响**。

我敢肯定，你至少曾因现代机器学习问题中涉及的**庞大特征数量**而感到震惊。每一个数据科学从业者，迟早都会面临这个挑战。本文将探讨最常用的降维算法的理论基础和 Python 实现：[**主成分分析**](https://en.wikipedia.org/wiki/Principal_component_analysis#:~:text=Principal%20component%20analysis%20(PCA)%20is,the%20visualization%20of%20multidimensional%20data.)（PCA）。

> 为什么我们需要减少特征数量？

现在涉及成千上万甚至百万特征的数据集已经很常见。向数据集中添加新特征可能会带来有价值的信息，但它们也会**减缓训练过程**并使...
