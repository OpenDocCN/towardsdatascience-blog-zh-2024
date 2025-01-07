# Kolmogorov-Arnold Networks（KAN）用于时间序列预测

> 原文：[`towardsdatascience.com/kolmogorov-arnold-networks-kans-for-time-series-forecasting-9d49318c3172?source=collection_archive---------1-----------------------#2024-05-14`](https://towardsdatascience.com/kolmogorov-arnold-networks-kans-for-time-series-forecasting-9d49318c3172?source=collection_archive---------1-----------------------#2024-05-14)

## 了解 Kolmogorov-Arnold Networks（KAN），并使用 Python 将其应用于时间序列预测

[](https://medium.com/@marcopeixeiro?source=post_page---byline--9d49318c3172--------------------------------)![Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--9d49318c3172--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9d49318c3172--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d49318c3172--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--9d49318c3172--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d49318c3172--------------------------------) ·阅读时长 11 分钟·2024 年 5 月 14 日

--

![](img/bb3c6be08cbcefbf664344f9958747ee.png)

图片由[Eduardo Bergen](https://unsplash.com/@eduardb?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

多层感知器（MLP）是深度学习模型的基础结构之一。它也是许多先进预测模型的构建模块，如 N-BEATS、NHiTS 和 TSMixer。

2024 年 4 月 30 日，论文[KAN: Kolmogorov-Arnold Network](https://arxiv.org/abs/2404.19756)发表，吸引了许多深度学习领域从业者的关注。在这篇论文中，作者提出了一种替代 MLP 的模型：**K**olmogorov-**A**rnold **N**etwork，简称**KAN**。

KAN 不是使用权重和固定的激活函数，而是使用可学习的函数，这些函数通过样条进行参数化。研究人员认为，KAN 可以在参数较少的情况下比 MLP 更加精确。

在这篇文章中，我们首先探索了样条（splines），因为它们帮助我们理解 KAN 的架构和关键元素。然后，我们深入研究 KAN 的内部工作机制。最后，我们将 KAN 应用于时间序列预测，并与标准的 MLP 和 N-BEATS 模型进行性能评估。

要了解更多关于 KAN 的细节，请务必阅读[原始论文](https://arxiv.org/abs/2404.19756)。
