# TimesFM：基础模型在时间序列预测中的兴起

> 原文：[https://towardsdatascience.com/timesfm-the-boom-of-foundation-models-in-time-series-forecasting-29701e0b20b5?source=collection_archive---------2-----------------------#2024-09-20](https://towardsdatascience.com/timesfm-the-boom-of-foundation-models-in-time-series-forecasting-29701e0b20b5?source=collection_archive---------2-----------------------#2024-09-20)

## 探索谷歌最新的人工智能模型如何利用超过3070亿个数据点实现零样本预测精度

[](https://medium.com/@luisroque?source=post_page---byline--29701e0b20b5--------------------------------)[![Luís Roque](../Images/e281d470b403375ba3c6f521b1ccf915.png)](https://medium.com/@luisroque?source=post_page---byline--29701e0b20b5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--29701e0b20b5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--29701e0b20b5--------------------------------) [Luís Roque](https://medium.com/@luisroque?source=post_page---byline--29701e0b20b5--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--29701e0b20b5--------------------------------) ·阅读时长12分钟·2024年9月20日

--

*本文由Rafael Guedes共同撰写。*

# 引言

预测是各行业中最重要的应用之一。一个例子是零售行业。多个规划活动需要预测能力，这有助于优化利润率，例如财务、生产或人力资源规划。这可以影响库存管理，比如浪费、剩余物品或缺货情况、客户服务水平以及整体决策。

开发一个准确的预测模型以支持上述过程，需要对最先进（SOTA）的预测方法有深入的理解。同时，还需要了解特定的业务领域知识，这些方法会被应用于其中。这两个因素推动了预训练模型越来越受到关注——它们减少了高度定制设置的需求。将这一动机与大规模预训练模型在自然语言处理（NLP）领域的成功结合，也就是大型语言模型（LLMs），我们就得到了一个有着许多贡献者的研究路径。

从理论上讲，我们知道语言任务和时间序列任务之间有一些相似性，例如数据是顺序的。另一方面，一个关键的区别是……
