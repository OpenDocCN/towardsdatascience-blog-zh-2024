# 用于估计潜伏期的EpiLPS

> 原文：[https://towardsdatascience.com/epilps-for-estimation-of-incubation-times-461aab54ff4b?source=collection_archive---------9-----------------------#2024-08-01](https://towardsdatascience.com/epilps-for-estimation-of-incubation-times-461aab54ff4b?source=collection_archive---------9-----------------------#2024-08-01)

## 探索如何使用{EpiLPS} R软件包来估计各种疾病的潜伏期。了解其在流行病学研究中的应用、方法论和好处，以便更准确地预测和改进公共卫生规划。

[](https://antoinesoetewey.medium.com/?source=post_page---byline--461aab54ff4b--------------------------------)[![安托万·索特韦](../Images/51d7837d18ff15a62cac2343a485e35d.png)](https://antoinesoetewey.medium.com/?source=post_page---byline--461aab54ff4b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--461aab54ff4b--------------------------------)[![走向数据科学](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--461aab54ff4b--------------------------------) [安托万·索特韦](https://antoinesoetewey.medium.com/?source=post_page---byline--461aab54ff4b--------------------------------)

·发表在[走向数据科学](https://towardsdatascience.com/?source=post_page---byline--461aab54ff4b--------------------------------) ·6 分钟阅读·2024年8月1日

--

# 动机

哈塞尔特大学数据科学研究所（DSI）的一组研究人员开发了一种新的统计模型，基于粗略数据估计病原微生物的潜伏期。传染病的潜伏期（定义为感染和首次出现症状之间的时间间隔）非常重要，因为它可以揭示疾病的流行潜力，并优化隔离期的长度以阻止传播。最近在*美国流行病学杂志*上发表了文章[(Gressani等人，2024)](https://doi.org/10.1093/aje/kwae192)，该文章通过[EpiLPS软件包](https://statsandr.com/blog/paper-epilps-a-fast-and-flexible-bayesian-tool-for-estimation-of-the-time-varying-reproduction-number/) [(Gressani等人，2022)](https://doi.org/10.1371/journal.pcbi.1010618) 实现了该方法的实际应用。

# 粗略数据

首先，估计潜伏期时间如此具有挑战性的原因是什么？魔鬼在于数据。真实的感染时间是隐秘的，很少被观察到。在信息论术语中，这种现象被称为“信息不完全”，但统计学家更倾向于称之为截尾。更准确地说，感染时间是…
