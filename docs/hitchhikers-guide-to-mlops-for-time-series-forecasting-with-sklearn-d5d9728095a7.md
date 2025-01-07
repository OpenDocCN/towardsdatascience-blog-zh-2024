# 费曼的《时间序列预测的MLOps指南》

> 原文：[https://towardsdatascience.com/hitchhikers-guide-to-mlops-for-time-series-forecasting-with-sklearn-d5d9728095a7?source=collection_archive---------2-----------------------#2024-04-04](https://towardsdatascience.com/hitchhikers-guide-to-mlops-for-time-series-forecasting-with-sklearn-d5d9728095a7?source=collection_archive---------2-----------------------#2024-04-04)

## 如何使用Scikit-Learn开发时间序列预测项目

[](https://medium.com/@cerlymarco?source=post_page---byline--d5d9728095a7--------------------------------)[![Marco Cerliani](../Images/ddc7943bfef3a7d59e36cc525dd5442e.png)](https://medium.com/@cerlymarco?source=post_page---byline--d5d9728095a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d5d9728095a7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d5d9728095a7--------------------------------) [Marco Cerliani](https://medium.com/@cerlymarco?source=post_page---byline--d5d9728095a7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d5d9728095a7--------------------------------) ·6分钟阅读·2024年4月4日

--

![](../Images/c111d001a7d56cf9f29e4031fdc9794e.png)

照片来自[Roger Ce](https://unsplash.com/fr/@roger_ce77?utm_source=medium&utm_medium=referral) 通过[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在数据驱动决策的世界里，时间序列预测通过利用历史数据模式来预测多个业务的未来结果，发挥着关键作用。无论你从事资产风险管理、交易、天气预测、能源需求预测还是交通分析，准确预测的能力对成功至关重要。

时间序列预测项目的成功不仅仅通过预测模型的拟合度来衡量。AI驱动工具在实际应用中的有效性还取决于参与的各种角色或工具之间的合作水平。为了实现最顺畅的合作程度，必须尽早从初期开发阶段引入一套规则和最佳实践。

![](../Images/3454e8b716b1083aab75e8fbec218c28.png)

MLOps组件 [图像由作者提供]

这些规则被称为机器学习操作（MLOps）。

> MLOps旨在将机器学习项目的各种元素统一成一个单一的、和谐的结构，努力维持这一点……
