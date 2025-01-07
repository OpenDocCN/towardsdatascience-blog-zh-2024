# ARIMA：一种预测时间序列数据的模型

> 原文：[https://towardsdatascience.com/arima-a-model-to-predict-time-series-data-a34c7638310b?source=collection_archive---------1-----------------------#2024-10-30](https://towardsdatascience.com/arima-a-model-to-predict-time-series-data-a34c7638310b?source=collection_archive---------1-----------------------#2024-10-30)

## 学习ARIMA模型如何工作，并学习如何在Python中实现它们以进行准确预测

[](https://medium.com/@niklas_lang?source=post_page---byline--a34c7638310b--------------------------------)[![Niklas Lang](../Images/5fa71386db00d248438c588c5ae79c67.png)](https://medium.com/@niklas_lang?source=post_page---byline--a34c7638310b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a34c7638310b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a34c7638310b--------------------------------) [Niklas Lang](https://medium.com/@niklas_lang?source=post_page---byline--a34c7638310b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a34c7638310b--------------------------------) ·14分钟阅读·2024年10月30日

--

![](../Images/65146ab6d4930b8a9a5cac3a1ddb513f.png)

图片由[Jean-Luc Picard](https://unsplash.com/@jeanlucbe?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

缩写ARIMA代表自回归积分滑动平均，是一类用于分析时间序列数据的统计模型。这个模型可以用来预测数据未来的发展趋势，例如在科学或技术领域。ARIMA方法主要用于存在所谓的时间自相关的情况，也就是说，简单来说，时间序列表现出某种趋势。

在本文中，我们将解释与ARIMA模型相关的所有方面，从时间序列数据及其特殊特征的简单介绍开始，直到在文章末尾训练我们自己的模型并进行详细评估。

# 什么是时间序列数据？

[时间序列数据](https://databasecamp.de/en/data/time-series-data)是一种特殊形式的数据集，其中测量是按规律的时间间隔进行的。这使得这样的数据集合具有其他数据集所缺少的一个额外维度，即时间维度。时间序列数据通常用于金融和经济领域，或者在自然科学中，当需要测量一个系统随时间变化时。
