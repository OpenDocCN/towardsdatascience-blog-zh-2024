# TimesFM：谷歌的时间序列预测基础模型

> 原文：[`towardsdatascience.com/timesfm-googles-foundation-model-for-time-series-forecasting-593a332dd08d?source=collection_archive---------1-----------------------#2024-02-28`](https://towardsdatascience.com/timesfm-googles-foundation-model-for-time-series-forecasting-593a332dd08d?source=collection_archive---------1-----------------------#2024-02-28)

## 时间序列的新时代

[](https://medium.com/@nikoskafritsas?source=post_page---byline--593a332dd08d--------------------------------)![Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--593a332dd08d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--593a332dd08d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--593a332dd08d--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--593a332dd08d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--593a332dd08d--------------------------------) ·阅读时间 9 分钟·2024 年 2 月 28 日

--

![](img/d8389aaf25ebe8fdbae11b592e9f898f.png)

作者使用 DALLE*3 创作。

*加入* [*AI Horizon Forecast*](https://aihorizonforecast.substack.com/subscribe)*，这是一个将复杂的 AI 话题讲解得清晰明了的博客。*

谷歌刚刚加入了时间序列预测基础模型的竞赛。

2023 年 8 月，时间序列社区因 *TimeGPT* 的发布而受到震动，这是 Nixtla 公司推出的第一个用于时间序列预测的基础模型。

紧随其后的是 *TimeGPT*，多个基础预报模型相继发布，但其中有一个脱颖而出。最近，谷歌发布了[***TimesFM***](https://arxiv.org/pdf/2310.10688.pdf)***[1]***，这是一个开创性的时间序列模型，取得了惊人的成果。

时间序列无处不在，广泛应用于零售、能源需求、经济学、医疗健康等多个领域。基础的时间序列模型可以高效地应用于任何时间序列案例，且准确性极高，就像 GPT-4 在文本处理中的应用一样。

**在本文中，我们讨论：**

1.  与自然语言处理（NLP）相比，时间序列中的基础模型面临的挑战。

1.  *TimesFM* 如何克服这些挑战。

1.  *TimesFM* 如何运作及其为何是一个强大的模型。

1.  *TimesFM* 基准结果。

1.  时间序列预测中基础模型的未来前景。
