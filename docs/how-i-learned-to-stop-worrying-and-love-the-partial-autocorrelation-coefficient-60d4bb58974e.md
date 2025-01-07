# 我如何学会停止担忧并爱上偏自相关系数

> 原文：[https://towardsdatascience.com/how-i-learned-to-stop-worrying-and-love-the-partial-autocorrelation-coefficient-60d4bb58974e?source=collection_archive---------4-----------------------#2024-06-26](https://towardsdatascience.com/how-i-learned-to-stop-worrying-and-love-the-partial-autocorrelation-coefficient-60d4bb58974e?source=collection_archive---------4-----------------------#2024-06-26)

![](../Images/111071cc8a5218d7ca171d13e8e9b5d1.png)

2015年8月，太平洋是地球上你最不想待的地方。向[厄尔尼诺](https://www.climate.gov/enso)致敬！来源：[NOAA](https://www.climate.gov/media/6413)

## 除了显而易见的对《奇爱博士》的致敬之外，我们将学习如何使用 PACF 精准地选择最具影响力的回归变量。

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--60d4bb58974e--------------------------------)[![Sachin Date](../Images/bd023298b414caf88f79b00ef032d065.png)](https://timeseriesreasoning.medium.com/?source=post_page---byline--60d4bb58974e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60d4bb58974e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--60d4bb58974e--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--60d4bb58974e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60d4bb58974e--------------------------------) ·阅读时长21分钟·2024年6月26日

--

作为一个概念，偏相关系数适用于时间序列数据和横截面数据。在时间序列分析中，它通常被称为**偏自相关系数**。在本文中，我将更多地关注**偏自相关系数**及其在配置自回归（AR）模型时的应用，特别是在它如何帮助你剔除**[不相关的回归变量](https://medium.com/towards-data-science/what-happens-when-you-include-irrelevant-variables-in-your-regression-model-77ab614f7073)**，从而改进你的 AR 模型。

在接下来的部分，我将解释：

1.  为什么你需要偏相关系数（PACF），

1.  如何计算偏（自）相关系数和偏自相关函数，

1.  如何判断偏（自）相关系数是否具有统计显著性，以及

1.  PACF 在构建自回归时间序列模型中的应用。

我还将解释偏相关的概念如何应用于构建线性……
