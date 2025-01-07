# Kolmogorov-Arnold Networks（KAN）用于时间序列预测

> 原文：[https://towardsdatascience.com/kolmogorov-arnold-networks-kans-for-time-series-forecasting-9d49318c3172?source=collection_archive---------1-----------------------#2024-05-14](https://towardsdatascience.com/kolmogorov-arnold-networks-kans-for-time-series-forecasting-9d49318c3172?source=collection_archive---------1-----------------------#2024-05-14)

## 了解Kolmogorov-Arnold Networks（KAN），并使用Python将其应用于时间序列预测

[](https://medium.com/@marcopeixeiro?source=post_page---byline--9d49318c3172--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--9d49318c3172--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9d49318c3172--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9d49318c3172--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--9d49318c3172--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d49318c3172--------------------------------) ·阅读时长11分钟·2024年5月14日

--

![](../Images/bb3c6be08cbcefbf664344f9958747ee.png)

图片由[Eduardo Bergen](https://unsplash.com/@eduardb?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

多层感知器（MLP）是深度学习模型的基础结构之一。它也是许多先进预测模型的构建模块，如[N-BEATS](/the-easiest-way-to-forecast-time-series-using-n-beats-d778fcc2ba60)、[NHiTS](/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)和[TSMixer](/tsmixer-the-latest-forecasting-model-by-google-2fd1e29a8ccb)。

2024年4月30日，论文[KAN: Kolmogorov-Arnold Network](https://arxiv.org/abs/2404.19756)发表，吸引了许多深度学习领域从业者的关注。在这篇论文中，作者提出了一种替代MLP的模型：**K**olmogorov-**A**rnold **N**etwork，简称**KAN**。

KAN不是使用权重和固定的激活函数，而是使用可学习的函数，这些函数通过样条进行参数化。研究人员认为，KAN可以在参数较少的情况下比MLP更加精确。

在这篇文章中，我们首先探索了样条（splines），因为它们帮助我们理解KAN的架构和关键元素。然后，我们深入研究KAN的内部工作机制。最后，我们将KAN应用于时间序列预测，并与标准的MLP和N-BEATS模型进行性能评估。

要了解更多关于KAN的细节，请务必阅读[原始论文](https://arxiv.org/abs/2404.19756)。
