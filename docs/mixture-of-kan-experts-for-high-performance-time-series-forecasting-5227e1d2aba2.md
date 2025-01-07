# 高性能时间序列预测的KAN专家混合模型

> 原文：[https://towardsdatascience.com/mixture-of-kan-experts-for-high-performance-time-series-forecasting-5227e1d2aba2?source=collection_archive---------0-----------------------#2024-09-11](https://towardsdatascience.com/mixture-of-kan-experts-for-high-performance-time-series-forecasting-5227e1d2aba2?source=collection_archive---------0-----------------------#2024-09-11)

## 探索RMoK模型及其架构，并使用Python进行小规模实验。

[](https://medium.com/@marcopeixeiro?source=post_page---byline--5227e1d2aba2--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--5227e1d2aba2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5227e1d2aba2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5227e1d2aba2--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--5227e1d2aba2--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5227e1d2aba2--------------------------------) ·10分钟阅读·2024年9月11日

--

![](../Images/4113b5de4b93ff2b0fd2add5e5dbfc73.png)

图片来自[Kyaw Tun](https://unsplash.com/@kyawthutun?utm_source=medium&utm_medium=referral)，[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Kolmogorov-Arnold网络（KAN）的引入为深度学习领域做出了重要贡献，因为它代表了多层感知器（MLP）的替代方案。

MLP当然是许多深度学习模型的构建模块，包括像[N-BEATS](/the-easiest-way-to-forecast-time-series-using-n-beats-d778fcc2ba60)、[NHiTS](/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)和[TSMixer](/tsmixer-the-latest-forecasting-model-by-google-2fd1e29a8ccb)这样最先进的预测方法。

然而，在一个使用KAN、MLP、NHiTS和NBEATS的[预测基准测试](https://medium.com/towards-data-science/kolmogorov-arnold-networks-kans-for-time-series-forecasting-9d49318c3172)中，我们发现KAN通常非常慢，并且在各种预测任务中表现持续较差。需要注意的是，该基准测试是在M3和M4数据集上进行的，这些数据集包含超过99,000个独特的时间序列，频率范围从每小时到每年。

最终，当时应用KAN进行时间序列预测的结果令人失望，并不是一种推荐的做法。

现在，随着**可逆KAN混合模型**（RMoK）的提出，情况发生了变化，相关内容可以在论文中找到：[KAN4TSF: KAN及基于KAN的模型对时间序列预测有效吗？](https://arxiv.org/abs/2408.11306)

在本文中，我们首先探讨可逆混合KAN模型的架构和内部工作原理…
