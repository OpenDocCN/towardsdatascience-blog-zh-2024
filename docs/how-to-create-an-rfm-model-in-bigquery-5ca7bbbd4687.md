# 如何在BigQuery中创建RFM模型

> 原文：[https://towardsdatascience.com/how-to-create-an-rfm-model-in-bigquery-5ca7bbbd4687?source=collection_archive---------11-----------------------#2024-03-05](https://towardsdatascience.com/how-to-create-an-rfm-model-in-bigquery-5ca7bbbd4687?source=collection_archive---------11-----------------------#2024-03-05)

## 了解什么是RFM模型，如何创建一个，并如何根据结果进行细分

[](https://medium.com/@thomas.ellyatt?source=post_page---byline--5ca7bbbd4687--------------------------------)[![Tom Ellyatt](../Images/8756acdd11fef8db9a868820251e7575.png)](https://medium.com/@thomas.ellyatt?source=post_page---byline--5ca7bbbd4687--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ca7bbbd4687--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5ca7bbbd4687--------------------------------) [Tom Ellyatt](https://medium.com/@thomas.ellyatt?source=post_page---byline--5ca7bbbd4687--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ca7bbbd4687--------------------------------) ·阅读时间：8分钟·2024年3月5日

--

![](../Images/3d76c99202a24a052aee2d10dc315976.png)

通过DALL-E创建

## 在本文中，我们将讨论：

+   什么是RFM细分及其在营销中的重要性

+   如何在BigQuery中创建RFM分位数

+   如何根据RFM分位数创建RFM细分

+   创建你自己的RFM模型时需要考虑的事项

> ***注意 — 本文中使用的所有数据均为虚构，并由我在BigQuery中生成。**

## 让我们从基础开始，什么是RFM模型？

**RFM**（*最近度、频率、金额*）模型是一种客户细分技术，利用过去的购买行为将客户划分为不同的群体。

+   **最近度（Recency）**衡量客户最近一次购买的时间

+   **频率（Frequency）**评估客户交易的频率

+   **金额（Monetary）**衡量客户的消费金额

仅这三个细分可以帮助你更好地理解客户群体，但你可以将它们结合起来形成更多的细分。这些细分帮助你识别哪些客户是你的最佳客户，哪些客户正在流失，或者……
