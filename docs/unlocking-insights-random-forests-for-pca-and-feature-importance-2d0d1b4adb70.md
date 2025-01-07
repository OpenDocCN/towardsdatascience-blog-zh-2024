# 解锁洞察：随机森林在 PCA 和特征重要性中的应用

> 原文：[`towardsdatascience.com/unlocking-insights-random-forests-for-pca-and-feature-importance-2d0d1b4adb70?source=collection_archive---------6-----------------------#2024-03-31`](https://towardsdatascience.com/unlocking-insights-random-forests-for-pca-and-feature-importance-2d0d1b4adb70?source=collection_archive---------6-----------------------#2024-03-31)

## 一种经过验证的解决方案如何在处理日常机器学习问题时取得出色的成果

[](https://medium.com/@christopher_karg?source=post_page---byline--2d0d1b4adb70--------------------------------)![Christopher Karg](https://medium.com/@christopher_karg?source=post_page---byline--2d0d1b4adb70--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2d0d1b4adb70--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2d0d1b4adb70--------------------------------) [Christopher Karg](https://medium.com/@christopher_karg?source=post_page---byline--2d0d1b4adb70--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2d0d1b4adb70--------------------------------) ·12 分钟阅读·2024 年 3 月 31 日

--

![](img/cfbd74dc31cad6445c58b653ace8eacc.png)

来源：[`www.pexels.com/photo/a-tractor-on-a-crop-18410308/`](https://www.pexels.com/photo/a-tractor-on-a-crop-18410308/)

由于生成性 AI 和庞大神经网络的广泛关注，人们容易忽视那些经过验证的旧式机器学习算法（它们其实并不算很旧……）。我甚至敢说，对于大多数商业案例，直接的机器学习解决方案比复杂的 AI 实现更能取得成果。机器学习算法不仅在规模上表现出色，远低的模型复杂度也是它们在大多数场景中更具优势的原因（这是我个人的看法）。更不用说，我也更容易追踪这些机器学习解决方案的性能了。

在本文中，我们将使用一种经典的机器学习解决方案来解决一个经典的机器学习问题。更具体地说，我将展示如何通过几行代码使用随机森林分类器识别数据集中的特征重要性。我将首先演示此技术的有效性。然后，我将采用“回归基础”的方法，展示该方法是如何在后台工作的，通过从头开始创建决策树和随机森林，同时在此过程中对模型进行基准测试。
