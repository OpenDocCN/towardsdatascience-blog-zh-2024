# 连续排名概率分数（CRPS）用于预测的基本指南

> 原文：[`towardsdatascience.com/essential-guide-to-continuous-ranked-probability-score-crps-for-forecasting-ac0a55dcb30d?source=collection_archive---------4-----------------------#2024-08-31`](https://towardsdatascience.com/essential-guide-to-continuous-ranked-probability-score-crps-for-forecasting-ac0a55dcb30d?source=collection_archive---------4-----------------------#2024-08-31)

![](img/e435885a12392409123f076d51ebcb66.png)

图像由 Midjourney 生成

## 学习如何评估概率预测，以及 CRPS 如何与其他指标相关

[](https://eryk-lewinson.medium.com/?source=post_page---byline--ac0a55dcb30d--------------------------------)![Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--ac0a55dcb30d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ac0a55dcb30d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ac0a55dcb30d--------------------------------) [Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--ac0a55dcb30d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ac0a55dcb30d--------------------------------) ·7 分钟阅读·2024 年 8 月 31 日

--

如果我问你如何评估回归问题，你可能会列举出很多[**评估指标**](https://medium.com/towards-data-science/a-comprehensive-overview-of-regression-evaluation-metrics-6264af0926db)，比如 MSE、MAE、RMSE、MAPE 等。这些指标的共同点是它们都关注点预测。

当我们想要训练模型关注预测分布而不是单一的点时，情况会有所不同。在这种情况下，我们需要使用不同的指标，而这些指标在数据科学博客文章中不常见。

上次，我研究了[分位数损失（即弹球损失）](https://medium.com/towards-data-science/an-introduction-to-quantile-loss-a-k-a-the-pinball-loss-33cccac378a9)。这次，我将带你了解另一种用于评估概率预测的指标——连续排名概率分数（CRPS）。

# 下面是一些定义，帮助我们入门

第一个概念很简单，但我们仍然需要确保我们在同一页面上。**概率预测**提供了可能结果的分布。例如，点预测可能会预测明天的温度准确为 23°C，而概率模型可能会预测明天温度有 70% 的概率会在 20°C 到 25°C 之间。
