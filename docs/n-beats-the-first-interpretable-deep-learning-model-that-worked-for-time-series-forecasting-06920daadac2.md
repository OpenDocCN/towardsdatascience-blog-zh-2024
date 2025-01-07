# N-BEATS — 第一个在时间序列预测中有效的可解释深度学习模型

> 原文：[https://towardsdatascience.com/n-beats-the-first-interpretable-deep-learning-model-that-worked-for-time-series-forecasting-06920daadac2?source=collection_archive---------2-----------------------#2024-05-11](https://towardsdatascience.com/n-beats-the-first-interpretable-deep-learning-model-that-worked-for-time-series-forecasting-06920daadac2?source=collection_archive---------2-----------------------#2024-05-11)

## 易于理解的N-BEATS工作原理深度解析，以及你如何使用它。

[](https://medium.com/@jodancker?source=post_page---byline--06920daadac2--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--06920daadac2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--06920daadac2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--06920daadac2--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--06920daadac2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--06920daadac2--------------------------------) ·11分钟阅读·2024年5月11日

--

![](../Images/980ff84a5ed71008034c95d433f1ed18.png)

N-BEATS架构（图片来自[Oreshkin等人](https://arxiv.org/pdf/1905.10437)）。

时间序列预测一直是深度学习和Transformers未能超越其他模型的唯一领域。

从Makridakis M比赛来看，获胜的解决方案总是依赖于统计模型。直到M4比赛，获胜的解决方案才是纯统计模型或机器学习与统计模型的混合。纯机器学习方法几乎无法超越竞争基准。

这一切在2020年由[Oreshkin等人](https://arxiv.org/pdf/1905.10437)发表的一篇论文中发生了变化。作者们发布了N-BEATS，这是一种有前景的纯深度学习方法。该模型超越了M4竞赛的获胜方案。它是第一个超越成熟统计方法的纯深度学习方法。

N-BEATS代表**N**eural **B**asis **E**xpansion **A**nalysis for Interpretable **T**ime **S**eries（可解释的时间序列神经基础扩展分析）。

在本文中，我将介绍N-BEATS背后的架构。但请不要担心，深入讲解将会易于理解。我还将展示如何使深度学习方法具有可解释性。然而，仅仅理解N-BEATS如何工作是不够的。因此，我将向你展示如何…
