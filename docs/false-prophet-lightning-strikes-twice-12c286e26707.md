# 假预言者：闪电两次击中

> 原文：[https://towardsdatascience.com/false-prophet-lightning-strikes-twice-12c286e26707?source=collection_archive---------5-----------------------#2024-03-01](https://towardsdatascience.com/false-prophet-lightning-strikes-twice-12c286e26707?source=collection_archive---------5-----------------------#2024-03-01)

## 将外部天气数据融入基于Meta的Prophet时间序列回归模型的见解

[](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--12c286e26707--------------------------------)[![Bradley Stephen Shaw](../Images/b3ef5e6e292083ff0f8523ec5ffe89f0.png)](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--12c286e26707--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--12c286e26707--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--12c286e26707--------------------------------) [Bradley Stephen Shaw](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--12c286e26707--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12c286e26707--------------------------------) ·14分钟阅读·2024年3月1日

--

![](../Images/b711662bfae1897264979effb82c8159.png)

图片来自 [Michał Mancewicz](https://unsplash.com/@kreyatif?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

当我刚搬到英国时，我惊讶于人们谈论天气的频繁程度。来自亚热带地区¹的我，今天的天气和昨天几乎没有区别——简直是美极了。而且明天的天气还会继续如此。

现在，无论我们是否喜欢（这个双关语），环境条件的影响都会波及到我们可能感兴趣的预测内容：比如在阳光明媚的周末之前，一家商店应该准备多少冰淇淋，寒流期间家庭的能源使用，海滩停车位的可用性。还有更多。

尽管天气非常重要，但要准确预测天气通常是非常困难的——只需要问问我手机上的天气应用程序就知道了。今天我们不会让这个问题影响我们的进度，首先我们会看看如何将天气信息融入模型中以提高性能，然后再尝试用简单和不那么简单的方式预测*未来*的天气。

我们将基于之前关于时间序列回归的讨论进行扩展，和往常一样，我们将使用真实世界的数据。

# 大致情况
