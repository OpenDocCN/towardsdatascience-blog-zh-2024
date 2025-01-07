# 使用 TiDE 进行时间序列预测

> 原文：[https://towardsdatascience.com/time-series-forecasting-with-tide-b043acc60f79?source=collection_archive---------1-----------------------#2024-01-09](https://towardsdatascience.com/time-series-forecasting-with-tide-b043acc60f79?source=collection_archive---------1-----------------------#2024-01-09)

## 探索 TiDE 的架构，并在 Python 中应用于预测项目

[](https://medium.com/@marcopeixeiro?source=post_page---byline--b043acc60f79--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--b043acc60f79--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b043acc60f79--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b043acc60f79--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--b043acc60f79--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b043acc60f79--------------------------------) ·阅读时间：8分钟·2024年1月9日

--

![](../Images/7cabdb95a757fd61eb4ad3ac36b3d177.png)

图片由 [Boris Smokrovic](https://unsplash.com/@borisworkshop?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在我们对时间序列预测领域最新进展的探索中，我们发现了 [N-HiTS](https://medium.com/towards-data-science/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)、[PatchTST](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)、[TimeGPT](https://medium.com/towards-data-science/timegpt-the-first-foundation-model-for-time-series-forecasting-bf0a75e63b3a) 和 [TSMixer](https://medium.com/towards-data-science/tsmixer-the-latest-forecasting-model-by-google-2fd1e29a8ccb)。

尽管已有许多努力将 Transformer 架构应用于预测任务，但考虑到计算要求，实际表现只是中等水平。

实际上，简单的线性模型在许多基准数据集上已经证明优于复杂的基于 Transformer 的模型（参见 [Zheng et al., 2022](https://arxiv.org/pdf/2205.13504.pdf)）。

受此启发，2023年4月，谷歌的研究人员提出了 TiDE：一种基于多层感知机（MLP）的编码器-解码器架构的长期预测模型。

在他们的论文 [《使用 TiDE 进行长期预测：时间序列密集编码器》](https://arxiv.org/pdf/2304.08424.pdf)中，作者展示了该模型在多个数据集上的表现优于其他基于 Transformer 和 MLP 的模型，如 PatchTST 和 N-HiTS。

在本文中，我们首先探讨TiDE的架构和内部运作。然后，我们在Python中应用该模型，并将其用于我们自己的小型预测实验。
