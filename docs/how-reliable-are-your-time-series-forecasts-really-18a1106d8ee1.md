# 你的时间序列预测到底有多可靠？

> 原文：[https://towardsdatascience.com/how-reliable-are-your-time-series-forecasts-really-18a1106d8ee1?source=collection_archive---------3-----------------------#2024-04-05](https://towardsdatascience.com/how-reliable-are-your-time-series-forecasts-really-18a1106d8ee1?source=collection_archive---------3-----------------------#2024-04-05)

## 如何通过交叉验证、可视化和统计假设检验来揭示最佳的预测时间范围

[](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--18a1106d8ee1--------------------------------)[![Bradley Stephen Shaw](../Images/b3ef5e6e292083ff0f8523ec5ffe89f0.png)](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--18a1106d8ee1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--18a1106d8ee1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--18a1106d8ee1--------------------------------) [Bradley Stephen Shaw](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--18a1106d8ee1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--18a1106d8ee1--------------------------------) ·13分钟阅读·2024年4月5日

--

![](../Images/d0bbc87e34027cf7403c5178d06a8315.png)

图片由[Nigel Tadyanehondo](https://unsplash.com/@nxvision?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

想象一下，你拥有一颗水晶球——一个神秘的家族传家宝，代代相传。它显得有些老旧，清澈度和光泽早已不复存在，表面上还散落着一些缺口。

尽管它的来源模糊不清，但你在其中看到的事情似乎总会以某种方式变为现实，至少在短期内是如此。它经常向你展示未来的事件，但你到底能有多大程度上信任它呢？

我这里提到的水晶球当然是我们的时间序列模型，我们按照与Meta的Prophet套件相同的方法构建了这些模型。我调皮地将我的实现称为“假先知”，但它看起来绝非如此，所产生的预测结果看起来相当准确（而且我有交叉验证结果来证明这一点）。

然而，它毕竟只是一个模型，除了通常会出现错误外，模型还往往在极端情况和边缘情形下表现不佳；在这个背景下，极端情况指的是预测时间跨度极大的情况。

接下来，我们将构建一个时间序列模型来预测英国的道路交通事故……
