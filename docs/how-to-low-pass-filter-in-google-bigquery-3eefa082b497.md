# 如何在 Google BigQuery 中进行低通滤波

> 原文：[`towardsdatascience.com/how-to-low-pass-filter-in-google-bigquery-3eefa082b497?source=collection_archive---------6-----------------------#2024-01-21`](https://towardsdatascience.com/how-to-low-pass-filter-in-google-bigquery-3eefa082b497?source=collection_archive---------6-----------------------#2024-01-21)

## 在处理时间序列数据时，应用滤波器去除噪声可能非常重要。本篇文章展示了如何在 SQL / BigQuery 中实现低通滤波，这对于改进机器学习特征非常有用。

[](https://medium.com/@benjamin.thuerer?source=post_page---byline--3eefa082b497--------------------------------)![Benjamin Thürer](https://medium.com/@benjamin.thuerer?source=post_page---byline--3eefa082b497--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3eefa082b497--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3eefa082b497--------------------------------) [Benjamin Thürer](https://medium.com/@benjamin.thuerer?source=post_page---byline--3eefa082b497--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3eefa082b497--------------------------------) ·阅读时间：9 分钟·2024 年 1 月 21 日

--

时间序列数据的过滤是数据科学中最有用的预处理工具之一。实际上，数据几乎总是信号和噪声的结合，其中噪声不仅由缺乏周期性定义，还因为它没有代表感兴趣的信息。例如，假设你在关注零售店的日常访问。如果你关注的是季节性变化对访问的影响，你可能对由于工作日变化导致的短期模式不感兴趣（比如周六的访问量可能普遍高于周一，但那并不是你关心的重点）。

> 时间序列过滤是清理数据的一种工具

即使这看起来只是数据中的一个小问题，噪声或无关信息（比如短期的访问模式）也会显著增加特征复杂度，从而影响你的模型。如果不去除这些噪声，你的模型复杂度和训练数据量应该相应调整，以避免过拟合。
