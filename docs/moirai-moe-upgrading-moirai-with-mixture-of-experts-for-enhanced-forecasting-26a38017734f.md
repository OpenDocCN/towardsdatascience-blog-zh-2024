# MOIRAI-MOE：通过专家混合技术升级MOIRAI以增强预测能力

> 原文：[https://towardsdatascience.com/moirai-moe-upgrading-moirai-with-mixture-of-experts-for-enhanced-forecasting-26a38017734f?source=collection_archive---------3-----------------------#2024-11-02](https://towardsdatascience.com/moirai-moe-upgrading-moirai-with-mixture-of-experts-for-enhanced-forecasting-26a38017734f?source=collection_archive---------3-----------------------#2024-11-02)

## 这款流行的基础时间序列模型刚刚获得了更新。

[](https://medium.com/@nikoskafritsas?source=post_page---byline--26a38017734f--------------------------------)[![Nikos Kafritsas](../Images/de965cfcd8fbd8e1baf849017d365cbb.png)](https://medium.com/@nikoskafritsas?source=post_page---byline--26a38017734f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--26a38017734f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--26a38017734f--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--26a38017734f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--26a38017734f--------------------------------) ·阅读时间9分钟·2024年11月2日

--

![](../Images/c9226dcc182fdd8bd83c8b18f4665da4.png)

[*图片来源*](https://arxiv.org/pdf/2410.10469)

**争夺打造顶级基础预测模型的竞赛已经开始！**

Salesforce的***MOIRAI***，作为早期的基础模型之一，达到了高基准结果，并与其预训练数据集LOTSA一起开源。

我们在[这里](https://medium.com/towards-data-science/moirai-salesforces-foundation-model-for-time-series-forecasting-4eff6c34093d)深入分析了MOIRAI的工作原理——并构建了一个端到端的项目，将MOIRAI与流行的统计模型进行比较。

Salesforce现在发布了升级版——***MOIRAI-MOE***——具有显著的改进，特别是增加了**专家混合（MOE）**。我们曾在讨论另一个模型时简要提到过MOE，[Time-MOE](https://aihorizonforecast.substack.com/p/time-moe-billion-scale-time-series)也使用了多个专家。

在本文中，我们将讨论：

+   MOIRAI-MOE如何工作，以及为什么它是一个强大的模型。

+   MOIRAI和MOIRAI-MOE之间的主要区别。

+   MOIRAI-MOE如何利用专家混合提高预测准确性。

+   专家混合（Mixture-of-Experts）如何通常解决基础时间序列模型中的频率变化问题。

让我们开始吧。

> ✅ 我已推出[**AI Horizon Forecast**](https://aihorizonforecast.substack.com/)**，**这是一个专注于时间序列和创新的新闻通讯……
