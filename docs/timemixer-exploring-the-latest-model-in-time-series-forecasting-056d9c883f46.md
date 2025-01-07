# TimeMixer：探索时间序列预测中的最新模型

> 原文：[https://towardsdatascience.com/timemixer-exploring-the-latest-model-in-time-series-forecasting-056d9c883f46?source=collection_archive---------1-----------------------#2024-07-23](https://towardsdatascience.com/timemixer-exploring-the-latest-model-in-time-series-forecasting-056d9c883f46?source=collection_archive---------1-----------------------#2024-07-23)

## 发现并理解TimeMixer的内部工作原理，并在自己的预测项目中应用它，使用Python。

[](https://medium.com/@marcopeixeiro?source=post_page---byline--056d9c883f46--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--056d9c883f46--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--056d9c883f46--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--056d9c883f46--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--056d9c883f46--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--056d9c883f46--------------------------------) ·11分钟阅读·2024年7月23日

--

![](../Images/8fcc28bf4de815edcc5abb1c87844c6d.png)

图片来源：[sutirta budiman](https://unsplash.com/@sutirtab?utm_source=medium&utm_medium=referral) 通过[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

时间序列预测领域正在快速发展，许多模型不断被提出，并宣称具有最先进的性能。

深度学习模型如今已成为时间序列预测的常用方法，尤其是在处理具有多个特征的大型数据集时。

尽管近年来已经提出了许多模型，如[iTransformer](https://medium.com/towards-data-science/itransformer-the-latest-breakthrough-in-time-series-forecasting-d538ddc6c5d1)、[SOFTS](https://medium.com/towards-data-science/softs-the-latest-innovation-in-time-series-forecasting-dbc82553dd76)和[TimesNet](https://medium.com/towards-data-science/timesnet-the-latest-advance-in-time-series-forecasting-745b69068c9c)，但它们在其他基准测试中的表现往往不及像[NHITS](https://medium.com/towards-data-science/all-about-n-hits-the-latest-breakthrough-in-time-series-forecasting-a8ddcb27b0d5)、[PatchTST](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)和[TSMixer](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)等模型。

2024年5月，提出了一种新模型：TimeMixer。根据原始论文[TimeMixer: 可分解的多尺度混合用于时间序列预测](https://arxiv.org/abs/2405.14616)，该模型在基于MLP的架构中，结合特征混合和序列分解来进行预测。

在本文中，我们首先探索TimeMixer的内部工作原理，然后在短期和长期预测任务中运行我们自己的小型基准测试。

和往常一样，务必阅读[原始研究文章](https://arxiv.org/abs/2405.14616)以获取更多细节。

> 学习最新的时间序列…
