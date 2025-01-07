# MMM：用于市场营销组合建模和广告支出回报率（ROAS）的贝叶斯框架

> 原文：[https://towardsdatascience.com/mmm-bayesian-framework-for-marketing-mix-modeling-and-roas-ccade4005bd5?source=collection_archive---------1-----------------------#2024-06-06](https://towardsdatascience.com/mmm-bayesian-framework-for-marketing-mix-modeling-and-roas-ccade4005bd5?source=collection_archive---------1-----------------------#2024-06-06)

## 使用PyMC的贝叶斯框架来建模媒体渠道表现、广告支出回报率（ROAS）和预算分配

[](https://medium.com/@luisroque?source=post_page---byline--ccade4005bd5--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--ccade4005bd5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ccade4005bd5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ccade4005bd5--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--ccade4005bd5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ccade4005bd5--------------------------------) ·18分钟阅读·2024年6月6日

--

*这篇文章由Rafael Guedes共同撰写。*

# 介绍

可扩展的互联网企业依赖营销来推动增长。当然，不仅如此，在一定规模下，极少数公司能够承受不在客户获取方面做到极其高效的成本。两大热门话题，企业正在大量投资以将人工智能（AI）能力引入营销领域，分别是媒体组合建模（MMM）和客户生命周期价值（LTV）预测。两者的目标都是提高企业在营销上的投资回报。本文将介绍MMM是什么以及应用MMM的最佳实践。

MMM是一种技术，允许营销团队衡量他们的投资影响及其如何促进转化。随着过去几年可用的广告平台激增，这项任务的复杂性迅速增加。这一现象将潜在客户分散到了不同的媒体渠道，这些渠道可以分为离线或在线两类。传统的离线渠道与数字支持脱节，可能包括报纸、广播、电视广告、优惠券以及展会上的摊位。在线渠道爆炸性增长，企业将它们结合使用…
