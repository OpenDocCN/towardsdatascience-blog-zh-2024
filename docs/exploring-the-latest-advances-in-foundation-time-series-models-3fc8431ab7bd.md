# 探索基础时间序列模型的最新进展

> 原文：[https://towardsdatascience.com/exploring-the-latest-advances-in-foundation-time-series-models-3fc8431ab7bd?source=collection_archive---------1-----------------------#2024-07-17](https://towardsdatascience.com/exploring-the-latest-advances-in-foundation-time-series-models-3fc8431ab7bd?source=collection_archive---------1-----------------------#2024-07-17)

## 快速准确地预测新数据——无需训练

[](https://medium.com/@nikoskafritsas?source=post_page---byline--3fc8431ab7bd--------------------------------)[![Nikos Kafritsas](../Images/de965cfcd8fbd8e1baf849017d365cbb.png)](https://medium.com/@nikoskafritsas?source=post_page---byline--3fc8431ab7bd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3fc8431ab7bd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3fc8431ab7bd--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--3fc8431ab7bd--------------------------------)

· 发表在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3fc8431ab7bd--------------------------------) · 8 分钟阅读 · 2024年7月17日

--

![](../Images/5e26a78575698dd1a34f917967ce67c7.png)

*加入* [*AI Horizon Forecast*](https://aihorizonforecast.substack.com/subscribe)*，这是一个将复杂的AI话题讲解得像白昼一样清晰的博客。*

基础时间序列模型的最新进展具有突破性意义。

TimeGPT 是第一个原生基础模型。它在去年8月发布，并震撼了预测社区。

自那时以来，许多其他基础模型也已发布，包括：

+   TimesFM

+   MOIRAI

+   微型时间混合器（TTM）

+   MOMENT

我们在之前的文章中讨论过这些模型，但自发布以来，它们已经有了许多更新。

在本文中，我们将探索这些更新——包括新的基准测试和改进后的模型变体。

让我们开始吧。

# TimesFM —— 谷歌的基础模型 [1]

> **新更新：** 最近模型的权重在 Hugging Face 上发布了！你可以在[***AI项目文件夹***](https://aihorizonforecast.substack.com/p/ai-projects)找到 TimesFM 的项目教程***！***

谷歌以 *TimesFM* 进入了基础模型的竞争，这是一个拥有2亿个参数的模型。
