# iTransformer：时间序列预测中的最新突破

> 原文：[https://towardsdatascience.com/itransformer-the-latest-breakthrough-in-time-series-forecasting-d538ddc6c5d1?source=collection_archive---------1-----------------------#2024-04-09](https://towardsdatascience.com/itransformer-the-latest-breakthrough-in-time-series-forecasting-d538ddc6c5d1?source=collection_archive---------1-----------------------#2024-04-09)

## 了解 iTransformer 的架构，并使用 Python 在一个小实验中应用该模型。

[](https://medium.com/@marcopeixeiro?source=post_page---byline--d538ddc6c5d1--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--d538ddc6c5d1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d538ddc6c5d1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d538ddc6c5d1--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--d538ddc6c5d1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d538ddc6c5d1--------------------------------) ·9分钟阅读·2024年4月9日

--

![](../Images/59147fdd489a18f62e87a7e649bdd52e.png)

图片来自[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

预测领域在基础模型方面取得了很多进展，自2024年初以来，像[Lag-LLaMA](/lag-llama-open-source-foundation-model-for-time-series-forecasting-9afdfaf2bd7c)、[Time-LLM](/time-llm-reprogram-an-llm-for-time-series-forecasting-e2558087b8ac)、[Chronos](/chronos-the-latest-time-series-forecasting-foundation-model-by-amazon-2687d641705a)和Moirai等模型相继被提出。

然而，它们的性能略显不足（关于可重复的基准测试，见[这里](https://github.com/Nixtla/nixtla/tree/main/experiments)），我认为目前数据特定的模型仍然是最优解。

为此，Transformer 架构已经以多种形式应用于时间序列预测，其中[PatchTST](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)在长时间范围预测中达到了最先进的性能。

持挑战性的PatchTST迎来了**iTransformer**模型，该模型在2024年3月由论文[iTransformer: Inverted Transformers Are Effective for Time Series Forecasting](https://arxiv.org/abs/2310.06625)提出。

在本文中，我们发现了iTransformer背后引人注目的简单概念，并探讨了其架构。接着，我们在一个小实验中应用该模型，并将其表现与[TSMixer](/tsmixer-the-latest-forecasting-model-by-google-2fd1e29a8ccb)、[N-HiTS](/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)和PatchTST进行比较。

欲了解更多细节，请务必阅读[原始论文](https://arxiv.org/abs/2310.06625)。

> 了解最新的时间序列…
