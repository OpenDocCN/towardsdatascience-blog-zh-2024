# TimeGPT vs TiDE：零样本推理是预测的未来还是仅仅是噱头？

> 原文：[`towardsdatascience.com/timegpt-vs-tide-is-zero-shot-inference-the-future-of-forecasting-or-just-hype-9063bdbe0b76?source=collection_archive---------3-----------------------#2024-03-14`](https://towardsdatascience.com/timegpt-vs-tide-is-zero-shot-inference-the-future-of-forecasting-or-just-hype-9063bdbe0b76?source=collection_archive---------3-----------------------#2024-03-14)

## 基础模型：时间序列预测中 TimeGPT 与 TiDE 的全面比较

[](https://medium.com/@luisroque?source=post_page---byline--9063bdbe0b76--------------------------------)![Luís Roque](https://medium.com/@luisroque?source=post_page---byline--9063bdbe0b76--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9063bdbe0b76--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9063bdbe0b76--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--9063bdbe0b76--------------------------------)

·发表于[《Towards Data Science》](https://towardsdatascience.com/?source=post_page---byline--9063bdbe0b76--------------------------------) ·阅读时间 12 分钟·2024 年 3 月 14 日

--

*本文由 Rafael Guedes 与我共同撰写。*

# 介绍

预测是人工智能（AI）在学术研究和工业应用中的核心领域之一。事实上，它可能是我们在各行各业中能找到的最普遍的挑战之一。准确预测未来的销售量和市场趋势对企业优化规划过程至关重要。这包括提升贡献利润、减少浪费、确保适当的库存水平、优化供应链，并总体提升决策能力。

开发一个预测模型是一个复杂且多方面的挑战。它需要对最先进（SOTA）预测方法和应用到的具体业务领域有深刻的理解。此外，预测引擎将作为组织内部的关键基础设施，支持各部门之间的广泛流程。例如：

+   **营销团队**利用该模型为即将到来的期间（如下个月或下个季度）的投资分配等战略决策提供信息。

+   **采购团队**利用该模型做出有关……的明智决策。
