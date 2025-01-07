# 时间序列回归的混合模型

> 原文：[`towardsdatascience.com/humpty-dumpty-and-time-series-mixed-model-time-series-regression-f25c17f94559?source=collection_archive---------4-----------------------#2024-01-13`](https://towardsdatascience.com/humpty-dumpty-and-time-series-mixed-model-time-series-regression-f25c17f94559?source=collection_archive---------4-----------------------#2024-01-13)

## 使用多种模型形式来捕捉并预测复杂时间序列的各个组成部分

[](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--f25c17f94559--------------------------------)![Bradley Stephen Shaw](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--f25c17f94559--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f25c17f94559--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f25c17f94559--------------------------------) [Bradley Stephen Shaw](https://bradley-stephen-shaw.medium.com/?source=post_page---byline--f25c17f94559--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f25c17f94559--------------------------------) ·16 分钟阅读·2024 年 1 月 13 日

--

![](img/ea42597212f839d8fbe30668a57a77bf.png)

照片由[Hunter Haley](https://unsplash.com/@hnhmarketing?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

最近我不得不修理后院的围栏。它很旧，木质的，一直在威胁着随时倒塌。夹杂着咒骂，我突然意识到，为了完成这项工作，我需要使用这么多工具，有时确实需要不止一个工具才能完成任务。

这与时间序列回归有什么关系？一般来说，关系不大。特别地，却有不少关系：今天我们将深入探讨如何使用混合模型进行时间序列分析和预测。或者换句话说——使用更多的模型工具来完成预测任务。

所以，不再废话太多，我们马上开始：

1.  再次回顾大局，稍微讨论一下混合模型。

1.  查看一些现实世界的数据。

1.  使用简单模型捕捉我们时间序列中的趋势。

1.  季节性三种方式：决策树、线性回归和经典时间序列。

1.  把胡姆蒂·邓普蒂拼回去，得到一个单一的时间序列预测。
