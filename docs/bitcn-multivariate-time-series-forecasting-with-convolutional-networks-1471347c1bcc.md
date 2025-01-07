# BiTCN：基于卷积网络的多变量时间序列预测

> 原文：[https://towardsdatascience.com/bitcn-multivariate-time-series-forecasting-with-convolutional-networks-1471347c1bcc?source=collection_archive---------6-----------------------#2024-05-01](https://towardsdatascience.com/bitcn-multivariate-time-series-forecasting-with-convolutional-networks-1471347c1bcc?source=collection_archive---------6-----------------------#2024-05-01)

## 了解BiTCN模型在多变量时间序列预测中的应用，探索其架构，并在Python中实现它。

[](https://medium.com/@marcopeixeiro?source=post_page---byline--1471347c1bcc--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--1471347c1bcc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1471347c1bcc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1471347c1bcc--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--1471347c1bcc--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1471347c1bcc--------------------------------) ·10分钟阅读·2024年5月1日

--

![](../Images/b6f29593a7a517847ba11694535f6380.png)

摄影：由[Timothy Dykes](https://unsplash.com/@timothycdykes?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在时间序列预测领域，模型的架构通常依赖于多层感知机（MLP）或Transformer架构。

基于多层感知机（MLP）的模型，如[N-HiTS](/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)、[TiDE](/time-series-forecasting-with-tide-b043acc60f79)和[TSMixer](/tsmixer-the-latest-forecasting-model-by-google-2fd1e29a8ccb)，可以在保持快速训练的同时，取得非常好的预测性能。

另一方面，基于Transformer的模型，如[PatchTST](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)和[iTransformer](/itransformer-the-latest-breakthrough-in-time-series-forecasting-d538ddc6c5d1)也能取得良好的预测效果，但它们在内存使用上更加密集，且训练时间较长。

然而，在预测领域，卷积神经网络（CNN）这一架构仍然在很大程度上未被充分利用。

传统上，卷积神经网络（CNN）主要应用于计算机视觉领域，但在预测中的应用仍然稀缺，只有[TimesNet](https://medium.com/towards-data-science/timesnet-the-latest-advance-in-time-series-forecasting-745b69068c9c)是最近的一个例子。

然而，已证明卷积神经网络（CNN）在处理序列数据方面是有效的，并且其架构允许并行计算，这可以大大加快训练速度。

本文将探讨BiTCN，这是2023年3月在论文中提出的一个模型…
