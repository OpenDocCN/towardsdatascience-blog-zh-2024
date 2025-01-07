# SOFTS：时间序列预测的最新创新

> 原文：[https://towardsdatascience.com/softs-the-latest-innovation-in-time-series-forecasting-dbc82553dd76?source=collection_archive---------1-----------------------#2024-06-11](https://towardsdatascience.com/softs-the-latest-innovation-in-time-series-forecasting-dbc82553dd76?source=collection_archive---------1-----------------------#2024-06-11)

## 了解SOFTS的架构及其创新的STAD模块，并在Python中应用于预报项目。

[](https://medium.com/@marcopeixeiro?source=post_page---byline--dbc82553dd76--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--dbc82553dd76--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dbc82553dd76--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dbc82553dd76--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--dbc82553dd76--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dbc82553dd76--------------------------------) ·阅读时间9分钟·2024年6月11日

--

![](../Images/3a6c45b51732226821448499983c9249.png)

图片来源：[Vincent van Zalinge](https://unsplash.com/@vincentvanzalinge?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

近年来，深度学习已成功应用于时间序列预测，其中新的架构不断设定了最先进性能的新标准。

一切始于2020年的[N-BEATS](https://medium.com/towards-data-science/the-easiest-way-to-forecast-time-series-using-n-beats-d778fcc2ba60)，随后在2022年推出了[NHITS](https://medium.com/towards-data-science/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)。2023年，[PatchTST](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)和[TSMixer](https://medium.com/towards-data-science/tsmixer-the-latest-forecasting-model-by-google-2fd1e29a8ccb)相继提出，它们依然位列顶级预测模型之中。

最近，我们发现了[iTransformer](https://medium.com/towards-data-science/itransformer-the-latest-breakthrough-in-time-series-forecasting-d538ddc6c5d1)，它进一步提升了深度学习预测模型的性能。

现在，我们介绍**S**eries-c**O**re **F**used **T**ime **S**eries预报模型，简称**SOFTS**。

该模型在2024年4月发表于文章[SOFTS: Efficient Multivariate Time Series Forecasting with Series-Core Fusion](https://arxiv.org/pdf/2404.14197)，采用了一种集中式策略来学习不同序列之间的交互，从而在多元预测任务中取得了最先进的性能。

在本文中，我们将详细探讨SOFTS的架构，并发现一个新颖的**ST**ar **A**ggregate-**D**ispatch（**STAD**）模块，该模块负责学习时间序列之间的交互。接着，我们应用…
