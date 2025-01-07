# N-HiTS — 使深度学习在时间序列预测中更加高效

> 原文：[https://towardsdatascience.com/n-hits-making-deep-learning-for-time-series-forecasting-more-efficient-d00956fc3e93?source=collection_archive---------3-----------------------#2024-05-30](https://towardsdatascience.com/n-hits-making-deep-learning-for-time-series-forecasting-more-efficient-d00956fc3e93?source=collection_archive---------3-----------------------#2024-05-30)

## 深入探讨N-HiTS如何工作以及如何使用它

[](https://medium.com/@jodancker?source=post_page---byline--d00956fc3e93--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--d00956fc3e93--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d00956fc3e93--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d00956fc3e93--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--d00956fc3e93--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d00956fc3e93--------------------------------) ·10分钟阅读·2024年5月30日

--

![](../Images/425a28dbdf638a9e2e73b20022ce5f2a.png)

N-HiTS架构（图片来自 [Challu 和 Olivares 等人](https://arxiv.org/abs/2201.12886)）。

2020年，N-BEATS是第一个在时间序列预测中超越统计模型和混合模型的深度学习模型。

两年后的2022年，一款新模型将N-BEATS从其宝座上推翻了。[Challu 和 Olivares 等人](https://arxiv.org/abs/2201.12886)发布了深度学习模型N-HiTS。他们解决了N-BEATS在较长预测时间跨度上的两个短板：

+   降低准确度并

+   增加计算量。

N-HiTS代表 **N**eural **Hi**erarchical Interpolation for **T**ime **S**eries Forecasting。

该模型基于N-BEATS及其神经基扩展的思想。神经基扩展发生在多个跨层堆叠的块中。

在本文中，我将介绍N-HiTS背后的架构，特别是与N-BEATS的区别。但是不要担心，深入探讨将易于理解。不过，单单理解N-HiTS如何工作还不够。因此，我将向你展示如何在Python中轻松实现一个N-HiTS模型，并调整其超参数。

# **如果核心思想相同，N-BEATS和N-HiTS有什么区别呢**…
