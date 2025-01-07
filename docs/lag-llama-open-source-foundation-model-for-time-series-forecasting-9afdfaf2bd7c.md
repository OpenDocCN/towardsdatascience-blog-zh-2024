# Lag-Llama：开源时间序列预测基础模型

> 原文：[`towardsdatascience.com/lag-llama-open-source-foundation-model-for-time-series-forecasting-9afdfaf2bd7c?source=collection_archive---------5-----------------------#2024-02-13`](https://towardsdatascience.com/lag-llama-open-source-foundation-model-for-time-series-forecasting-9afdfaf2bd7c?source=collection_archive---------5-----------------------#2024-02-13)

## 探索 Lag-Llama 的架构，并学习如何在预测项目中使用 Python 来应用它

[](https://medium.com/@marcopeixeiro?source=post_page---byline--9afdfaf2bd7c--------------------------------)![Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--9afdfaf2bd7c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9afdfaf2bd7c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9afdfaf2bd7c--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--9afdfaf2bd7c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9afdfaf2bd7c--------------------------------) ·10 分钟阅读·2024 年 2 月 13 日

--

![](img/1ffb5208be254ace78666a5812b6a982.png)

图片由[Ray Hennessy](https://unsplash.com/@rayhennessy?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

2023 年 10 月，我发布了一篇关于[TimeGPT](https://medium.com/towards-data-science/timegpt-the-first-foundation-model-for-time-series-forecasting-bf0a75e63b3a)的文章，这是首个用于时间序列预测的基础模型之一，具备零-shot 推理、异常检测和符合预测能力。

然而，TimeGPT 是一个专有模型，仅通过 API 令牌进行访问。尽管如此，它激发了对时间序列基础模型的更多研究，因为与自然语言处理（NLP）和计算机视觉相比，这一领域的发展相对滞后。

快进到 2024 年 2 月，我们现在有了一个开源的时间序列预测基础模型：**Lag-Llama**。

在原始论文中：[Lag-Llama：面向概率时间序列预测的基础模型](https://time-series-foundation-models.github.io/lag-llama.pdf)，该模型被提出为一种通用的基础模型，用于单变量的概率预测。它由来自不同机构的大团队开发，这些机构包括摩根士丹利、ServiceNow、蒙特利尔大学、Mila-Quebec 和麦吉尔大学。

在本文中，我们将探索 Lag-Llama 的架构、功能以及它是如何训练的。然后我们将实际在预测项目中使用 Lag-Llama……
