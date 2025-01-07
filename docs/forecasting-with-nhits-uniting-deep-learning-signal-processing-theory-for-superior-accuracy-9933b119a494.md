# 使用 NHiTs 进行预测：将深度学习与信号处理理论结合，实现卓越的准确性

> 原文：[`towardsdatascience.com/forecasting-with-nhits-uniting-deep-learning-signal-processing-theory-for-superior-accuracy-9933b119a494?source=collection_archive---------3-----------------------#2024-10-10`](https://towardsdatascience.com/forecasting-with-nhits-uniting-deep-learning-signal-processing-theory-for-superior-accuracy-9933b119a494?source=collection_archive---------3-----------------------#2024-10-10)

## 适用于所有预测场景的高性能深度学习模型

[](https://medium.com/@nikoskafritsas?source=post_page---byline--9933b119a494--------------------------------)![Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--9933b119a494--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9933b119a494--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9933b119a494--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--9933b119a494--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9933b119a494--------------------------------) ·阅读时间 10 分钟·2024 年 10 月 10 日

--

![](img/b8e878a9683d13d5e9c432ac79fdc215.png)

图片来源[1]

***NHiTS*发布于两年前，并自那时以来在预测社区中引起了广泛关注。**

首先，它是一个多功能模型——可以接受过去的观测值、已知的未来输入以及静态外生变量。它可以应用于各类预测领域，包括能源需求、零售和金融市场。

它轻量级，但性能强大。与典型的深度学习模型依赖“叠加”隐藏层不同，该模型利用信号理论概念，通过最小的参数提升性能。

最后，它的**多速率信号采样**策略使模型能够捕捉复杂的频率模式——这对于金融预测等领域至关重要。该模型也可以用于概率预测。

在本文中，我们将详细解释*NHiTS*，分析其架构，并通过实际示例突出其优势和内部工作原理。

让我们开始吧。

> *✅* 在我的通讯的[**AI 项目文件夹**](https://aihorizonforecast.substack.com/p/ai-projects)中，找到关于 NHiTS 的实际项目（项目 2），以及其他酷炫的项目！别忘了订阅！
