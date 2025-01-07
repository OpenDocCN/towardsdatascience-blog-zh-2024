# 实时异常检测用于质量控制

> 原文：[https://towardsdatascience.com/real-time-anomaly-detection-for-quality-control-e6af28a3350d?source=collection_archive---------5-----------------------#2024-02-03](https://towardsdatascience.com/real-time-anomaly-detection-for-quality-control-e6af28a3350d?source=collection_archive---------5-----------------------#2024-02-03)

## 在行业中两年的经验总结

[](https://medium.com/@anthonycvn?source=post_page---byline--e6af28a3350d--------------------------------)[![Anthony Cavin](../Images/60a13c72d1390b74864cd12bc959eae9.png)](https://medium.com/@anthonycvn?source=post_page---byline--e6af28a3350d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e6af28a3350d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e6af28a3350d--------------------------------) [Anthony Cavin](https://medium.com/@anthonycvn?source=post_page---byline--e6af28a3350d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e6af28a3350d--------------------------------) ·阅读时长10分钟·2024年2月3日

--

![](../Images/94997be408610283291b5d9da76f60e7.png)

编码器和潜在空间中的图表示示例（图像来源：作者）

**场景**：一个高速生产线正在生产成千上万的产品。安装了两台相机，持续监控每个产品的质量。

**目标**：开发一种算法，能够尽可能快地检查每个产品。

**约束条件**：你有一个资源有限的边缘设备。

在这篇博客文章中，我们将通过*分而治之*的方式解决问题。首先从图像中提取有意义的特征，然后通过使用异常检测模型来从这些特征中检测异常值。

> 关键思想是学习视觉输入的低维表示，并利用这种表示来训练一个分类器，区分正常输入和异常输入。

我们将探讨一些有趣的特征提取方法，包括定向梯度直方图（HOG）、小波边缘检测和卷积神经网络（CNN）。

最后，我们将介绍两个我发现特别有用的库，用于基准测试和实现流数据中的算法——[PyOD](https://pyod.readthedocs.io/en/latest/) 和 [PySAD](https://pysad.readthedocs.io/en/latest/)。
