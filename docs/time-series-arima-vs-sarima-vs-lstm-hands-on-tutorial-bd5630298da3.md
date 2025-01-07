# 时间序列 — ARIMA vs. SARIMA vs. LSTM：实践教程

> 原文：[https://towardsdatascience.com/time-series-arima-vs-sarima-vs-lstm-hands-on-tutorial-bd5630298da3?source=collection_archive---------5-----------------------#2024-10-03](https://towardsdatascience.com/time-series-arima-vs-sarima-vs-lstm-hands-on-tutorial-bd5630298da3?source=collection_archive---------5-----------------------#2024-10-03)

## 让我们一步步预测未来。

[](https://medium.com/@fmnobar?source=post_page---byline--bd5630298da3--------------------------------)[![Farzad Nobar](../Images/2d75209693b712300e6f0796bd2487d0.png)](https://medium.com/@fmnobar?source=post_page---byline--bd5630298da3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bd5630298da3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bd5630298da3--------------------------------) [Farzad Nobar](https://medium.com/@fmnobar?source=post_page---byline--bd5630298da3--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bd5630298da3--------------------------------) ·14分钟阅读·2024年10月3日

--

![](../Images/dc5baac7ed9ed6903a0138beeb84101b.png)

图片来自[Djim Loic](https://unsplash.com/@loic?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/analog-clock-at-12-am-ft0-Xu4nTvA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在这篇文章中，我们将深入探讨时间序列预测的世界。预测未来时间对各种业务具有重要价值。例如，需求预测对在线零售商准备年终促销活动非常有帮助，以确保足够的库存满足即将到来的购物需求。在金融领域，股票交易员依赖复杂的预测模型来决定买卖哪些证券。而在更基本的场景中，我们依赖天气预报来决定是否带伞或穿雨衣，每天出门上班时。这些都是时间序列预测在我们生活中扮演重要角色的实例。

在这篇文章中，我们将讨论该领域中三种最常见的时间序列预测模型：

1.  自回归积分滑动平均模型（ARIMA）

1.  季节性自回归积分滑动平均模型（SARIMA）

1.  长短期记忆（LSTM）。

我们将通过动手实现每个预测模型，训练模型并使用每个已训练的模型生成预测结果，来学习每个预测模型。然后我们将查看这些模型的评估指标...
