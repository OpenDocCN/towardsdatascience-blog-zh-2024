# Time-MoE：最新的基础预测模型

> 原文：[`towardsdatascience.com/time-moe-the-latest-foundation-forecasting-model-47eaebb78837?source=collection_archive---------1-----------------------#2024-10-29`](https://towardsdatascience.com/time-moe-the-latest-foundation-forecasting-model-47eaebb78837?source=collection_archive---------1-----------------------#2024-10-29)

## 探索开源的大型时间模型 Time-MoE，并通过 Python 在一个小实验中应用它

[](https://medium.com/@marcopeixeiro?source=post_page---byline--47eaebb78837--------------------------------)![Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--47eaebb78837--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47eaebb78837--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47eaebb78837--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--47eaebb78837--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47eaebb78837--------------------------------) ·12 分钟阅读·2024 年 10 月 29 日

--

![](img/697897b1e4eca76bd8a0a710515f7bd3.png)

图片来源：[Irina Iriser](https://unsplash.com/@iriser?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

传统上，时间序列预测领域依赖于数据特定的模型，其中模型会在特定的数据集和任务上进行训练。如果数据或预测范围发生变化，模型也必须进行相应的调整。

自 2023 年 10 月以来，研究人员一直在积极开发基础预测模型。借助这些大型时间模型，单个模型现在可以处理来自不同领域、不同频率和几乎任何预测范围的不同预测任务。

这些大型时间模型包括：

+   TimeGPT，通过 API 进行访问，方便执行预测和微调，无需使用本地资源

+   Lag-Llama，一个开源的概率预测模型，通过滞后值构建特征

+   Chronos，一个基于 T5 的模型，通过标记化和量化将无界时间序列领域转化为有界语言领域

+   Moirai，一个支持外生特征的模型，并且首次公开分享他们的数据集 LOTSA，包含超过 270 亿个数据点。
