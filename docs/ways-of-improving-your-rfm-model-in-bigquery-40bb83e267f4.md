# 在 BigQuery 中改进你的 RFM 模型

> 原文：[https://towardsdatascience.com/ways-of-improving-your-rfm-model-in-bigquery-40bb83e267f4?source=collection_archive---------10-----------------------#2024-03-26](https://towardsdatascience.com/ways-of-improving-your-rfm-model-in-bigquery-40bb83e267f4?source=collection_archive---------10-----------------------#2024-03-26)

## 用于更好客户洞察的高级策略

[](https://medium.com/@thomas.ellyatt?source=post_page---byline--40bb83e267f4--------------------------------)[![Tom Ellyatt](../Images/8756acdd11fef8db9a868820251e7575.png)](https://medium.com/@thomas.ellyatt?source=post_page---byline--40bb83e267f4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40bb83e267f4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--40bb83e267f4--------------------------------) [Tom Ellyatt](https://medium.com/@thomas.ellyatt?source=post_page---byline--40bb83e267f4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40bb83e267f4--------------------------------) ·14 分钟阅读·2024 年 3 月 26 日

--

**RFM**（Recency, Frequency, Monetary）模型，以其简单性和易于实施的特点，依然是客户关系管理中的一款出色工具，能够提供有价值的客户行为洞察。

基于我之前文章“[**如何在 BigQuery 中创建 RFM 模型**](https://medium.com/towards-data-science/how-to-create-an-rfm-model-in-bigquery-5ca7bbbd4687)”的基础，在这篇文章中，我们将探讨如何改进该模型。

## 在这篇文章中我们将涵盖以下内容：

+   [**如何创建客户评分**](#24ba)

+   [**替代的 RFM 分组方法**](#b540)

+   [**向模型添加里程碑**](#4cd4)

+   [**扩展模型以包括电子邮件互动**](#2265)

+   [**构建月度 RFM 模型并追溯历史月份**](#38ab)

所以，你已经在 BigQuery 中启动并运行了你的 RFM 模型，将客户分为如 **冠军**、**潜在忠诚者**、**有丧失风险** 等类别。这是一个很好的开始，但我们可以将其提升到一个新的水平。

将你的客户分组虽然讲述了一个不错的故事，但我喜欢称之为 **客户评分** 的方法，可以通过一个直观的单一指标来增强模型的效果。

## 什么是客户评分，为什么需要它？

![](../Images/d26c7077397815a2cc243dc2eeac52ce.png)

通过 DALL-E 创建
